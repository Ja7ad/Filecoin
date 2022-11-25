# Job Tracker

By using a job tracker, you can track the duration of tasks done by workers and tracking real-time.

## How to use?

1. You can add the jobTracker function to your source for compiling and using (`/extern/sector-storage/worker_tracked.go`)

- The path to the jobTracker log can be set using an environment variable `LOTUS_MINER_JOB_LOG_PATH=/root` before run miner.
- You can check the status of jobs in real-time on the miner server with `tail -f /root/job-{date}.log`

## jobTracker function

```go
package sectorstorage

import (
	"context"
	"fmt"
	"os"
	"sync"
	"time"

	"github.com/ipfs/go-cid"
	"go.opencensus.io/stats"
	"go.opencensus.io/tag"

	"github.com/filecoin-project/go-state-types/abi"
	"github.com/filecoin-project/specs-storage/storage"

	"github.com/filecoin-project/lotus/extern/sector-storage/sealtasks"
	"github.com/filecoin-project/lotus/extern/sector-storage/storiface"
	"github.com/filecoin-project/lotus/metrics"
	logging "github.com/ipfs/go-log/v2"
)

// Job Tracker Log instance
var logJob = logging.Logger("jobTracker")
var jobFile *os.File

type trackedWork struct {
	job            storiface.WorkerJob
	worker         WorkerID
	workerHostname string
}

type workTracker struct {
	lk sync.Mutex

	done    map[storiface.CallID]struct{}
	running map[storiface.CallID]trackedWork

	// TODO: done, aggregate stats, queue stats, scheduler feedback
}

func jobTracker(hostname string, workerID string, sector string, taskType string, startTime time.Time) {
	var err error
	endTime := time.Now()
	duration := endTime.Sub(startTime).String()
	job := fmt.Sprintf("\n%v\t%v\t%v\t%v\t%v\t%v\t%v", hostname, workerID[:8], sector, taskType, startTime.Format("2006-01-02 15:04:05"), endTime.Format("2006-01-02 15:04:05"), duration)

	// Get job log path from environment variable
	// if path not setted, set path to default ~/
	getLogPath := os.Getenv("LOTUS_MINER_JOB_LOG_PATH")
	if len(getLogPath) == 0 {
		getLogPath = "~/"
	}

	// Create job log file
	fileName := fmt.Sprintf("%v/jobs-%v.log", getLogPath, string(time.Now().Format("2006-01-02")))
	_, err = os.Stat(fileName)
	if os.IsNotExist(err) {
		if jobFile, err = os.Create(fileName); err != nil {
			logJob.Warnf("cannot create jobs log file, got error %v", err.Error())
		}
		if _, err = jobFile.WriteString("HostName Worker Sector TaskType StartTime EndTime Duration\n"); err != nil {
			logJob.Warnf("cannot write into job log file, got error %v", err.Error())
		}
	} else {
		if jobFile == nil {
			if jobFile, err = os.OpenFile(fileName, os.O_APPEND|os.O_WRONLY, 0600); err != nil {
				logJob.Warnf("cannot open jobs log file, got error %v", err.Error())
			}
		}
	}

	// Append job done in job log file
	if _, err = jobFile.WriteString(job); err != nil {
		logJob.Warnf("cannot write into job log file, got error %v", err.Error())
	}

	logJob.Infof("Worker %v in host %v taskType %v with duration %v done", workerID[:8], hostname, taskType, duration)

}

func (wt *workTracker) onDone(ctx context.Context, callID storiface.CallID) {
	wt.lk.Lock()
	defer wt.lk.Unlock()

	t, ok := wt.running[callID]
	if !ok {
		wt.done[callID] = struct{}{}

		stats.Record(ctx, metrics.WorkerUntrackedCallsReturned.M(1))
		return
	}

	took := metrics.SinceInMilliseconds(t.job.Start)

	ctx, _ = tag.New(
		ctx,
		tag.Upsert(metrics.TaskType, string(t.job.Task)),
		tag.Upsert(metrics.WorkerHostname, t.workerHostname),
	)
	stats.Record(ctx, metrics.WorkerCallsReturnedCount.M(1), metrics.WorkerCallsReturnedDuration.M(took))

	// jobTracker
	jobTracker(t.workerHostname, t.worker.String(), t.job.Sector.Number.String(), t.job.Task.Short(), t.job.Start)

	delete(wt.running, callID)
}

func (wt *workTracker) track(ctx context.Context, wid WorkerID, wi storiface.WorkerInfo, sid storage.SectorRef, task sealtasks.TaskType) func(storiface.CallID, error) (storiface.CallID, error) {
	return func(callID storiface.CallID, err error) (storiface.CallID, error) {
		if err != nil {
			return callID, err
		}

		wt.lk.Lock()
		defer wt.lk.Unlock()

		_, done := wt.done[callID]
		if done {
			delete(wt.done, callID)
			return callID, err
		}

		wt.running[callID] = trackedWork{
			job: storiface.WorkerJob{
				ID:     callID,
				Sector: sid.ID,
				Task:   task,
				Start:  time.Now(),
			},
			worker:         wid,
			workerHostname: wi.Hostname,
		}

		ctx, _ = tag.New(
			ctx,
			tag.Upsert(metrics.TaskType, string(task)),
			tag.Upsert(metrics.WorkerHostname, wi.Hostname),
		)
		stats.Record(ctx, metrics.WorkerCallsStarted.M(1))

		return callID, err
	}
}

func (wt *workTracker) worker(wid WorkerID, wi storiface.WorkerInfo, w Worker) Worker {
	return &trackedWorker{
		Worker:     w,
		wid:        wid,
		workerInfo: wi,

		tracker: wt,
	}
}

func (wt *workTracker) Running() []trackedWork {
	wt.lk.Lock()
	defer wt.lk.Unlock()

	out := make([]trackedWork, 0, len(wt.running))
	for _, job := range wt.running {
		out = append(out, job)
	}

	return out
}

type trackedWorker struct {
	Worker
	wid        WorkerID
	workerInfo storiface.WorkerInfo

	tracker *workTracker
}

func (t *trackedWorker) SealPreCommit1(ctx context.Context, sector storage.SectorRef, ticket abi.SealRandomness, pieces []abi.PieceInfo) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTPreCommit1)(t.Worker.SealPreCommit1(ctx, sector, ticket, pieces))
}

func (t *trackedWorker) SealPreCommit2(ctx context.Context, sector storage.SectorRef, pc1o storage.PreCommit1Out) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTPreCommit2)(t.Worker.SealPreCommit2(ctx, sector, pc1o))
}

func (t *trackedWorker) SealCommit1(ctx context.Context, sector storage.SectorRef, ticket abi.SealRandomness, seed abi.InteractiveSealRandomness, pieces []abi.PieceInfo, cids storage.SectorCids) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTCommit1)(t.Worker.SealCommit1(ctx, sector, ticket, seed, pieces, cids))
}

func (t *trackedWorker) SealCommit2(ctx context.Context, sector storage.SectorRef, c1o storage.Commit1Out) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTCommit2)(t.Worker.SealCommit2(ctx, sector, c1o))
}

func (t *trackedWorker) FinalizeSector(ctx context.Context, sector storage.SectorRef, keepUnsealed []storage.Range) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTFinalize)(t.Worker.FinalizeSector(ctx, sector, keepUnsealed))
}

func (t *trackedWorker) AddPiece(ctx context.Context, sector storage.SectorRef, pieceSizes []abi.UnpaddedPieceSize, newPieceSize abi.UnpaddedPieceSize, pieceData storage.Data) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, sector, sealtasks.TTAddPiece)(t.Worker.AddPiece(ctx, sector, pieceSizes, newPieceSize, pieceData))
}

func (t *trackedWorker) Fetch(ctx context.Context, s storage.SectorRef, ft storiface.SectorFileType, ptype storiface.PathType, am storiface.AcquireMode) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, s, sealtasks.TTFetch)(t.Worker.Fetch(ctx, s, ft, ptype, am))
}

func (t *trackedWorker) UnsealPiece(ctx context.Context, id storage.SectorRef, index storiface.UnpaddedByteIndex, size abi.UnpaddedPieceSize, randomness abi.SealRandomness, cid cid.Cid) (storiface.CallID, error) {
	return t.tracker.track(ctx, t.wid, t.workerInfo, id, sealtasks.TTUnseal)(t.Worker.UnsealPiece(ctx, id, index, size, randomness, cid))
}

var _ Worker = &trackedWorker{}

```

## Log file output and lotus-miner log

job-{date}.log file output (The log was in devnet) :

```console
   HostName              Worker       Sector  TaskType                    StartTime                      EndTime              Duration

ip-172-31-36-231			653d3a96    			2   			AP			            2021-07-13 02:56:56						    2021-07-13 02:56:56			     2.577535ms
ip-172-31-36-231			653d3a96			2   			AP			            2021-07-13 02:56:56						    2021-07-13 02:56:56			     2.954964ms
ip-172-31-36-231			8a4acfbf			2   			GET			            2021-07-13 02:56:56						    2021-07-13 02:56:56			     6.032642ms
ip-172-31-36-231			8a4acfbf			2   			PC1			            2021-07-13 02:56:56						    2021-07-13 02:56:56			     9.51718ms
ip-172-31-36-231			8a4acfbf			2   			GET			            2021-07-13 02:56:56						    2021-07-13 02:56:56			     749.349µs
ip-172-31-36-231			8a4acfbf    			2   			PC2						    2021-07-13 02:56:56						    2021-07-13 02:56:56			     43.170879ms
ip-172-31-36-231			653d3a96    			3    			AP						    2021-07-13 02:57:32						    2021-07-13 02:57:32			     2.824861ms
ip-172-31-36-231			653d3a96    			3   			AP						    2021-07-13 02:58:03						    2021-07-13 02:58:03			     3.147509ms
ip-172-31-36-231			8a4acfbf    			3   			GET						    2021-07-13 02:58:03						    2021-07-13 02:58:03			     6.214147ms
ip-172-31-36-231			8a4acfbf    			3   			PC1						    2021-07-13 02:58:03						    2021-07-13 02:58:03			     10.630998ms
ip-172-31-36-231			8a4acfbf    			3   			GET						    2021-07-13 02:58:03						    2021-07-13 02:58:03			     949.144µs
ip-172-31-36-231			8a4acfbf    			3   			PC2						    2021-07-13 02:58:03						    2021-07-13 02:58:03			     15.417489ms
ip-172-31-36-231			8a4acfbf    			2   			GET						    2021-07-13 02:59:56						    2021-07-13 02:59:56			     930.484µs
ip-172-31-36-231			8a4acfbf    			3   			GET						    2021-07-13 02:59:56						    2021-07-13 02:59:56			     641.816µs
ip-172-31-36-231			8a4acfbf    			2   			C1						    2021-07-13 02:59:56						    2021-07-13 02:59:56			     9.955921ms
ip-172-31-36-231			8a4acfbf    			3   			C1						    2021-07-13 02:59:56						    2021-07-13 02:59:56			     13.982852ms
ip-172-31-36-231			8a4acfbf    			2   			C2						    2021-07-13 02:59:56						    2021-07-13 03:01:20			     1m24.000035782s
ip-172-31-36-231			8a4acfbf    			3   			C2						    2021-07-13 02:59:56						    2021-07-13 03:01:21			     1m25.296810287s
ip-172-31-36-231			8a4acfbf    			3   			GET						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     1.213411ms
ip-172-31-36-231			8a4acfbf    			2   			GET						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     12.71562ms
ip-172-31-36-231			8a4acfbf    			3   			FIN						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     1.744864ms
ip-172-31-36-231			8a4acfbf    			2   			FIN						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     1.557189ms
ip-172-31-36-231			653d3a96    			3   			GET						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     61.848277ms
ip-172-31-36-231			653d3a96    			2   			GET						    2021-07-13 03:02:36						    2021-07-13 03:02:36			     62.414792ms
```

lotus-miner log :

```console
2021-07-13T02:56:56.111-0400    INFO    jobTracker      sector-storage/worker_tracked.go:75     Worker 8a4acfbf in hostname ip-172-31-36-231 taskType PC1 with duration 9.51718ms done
```
