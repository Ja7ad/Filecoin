# Scheduler Windows

```json
"level":"debug","ts":"2021-05-10T05:36:44.932+1000","logger":"advmgr","caller":"sector-storage/sched.go:449","msg":"SCHED windows: [{allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]} {allocated:{memUsedMin:0 memUsedMax:0 gpuUsed:false cpuUse:0 cond:<nil>} todo:[]}]"}
```

## Scheduler Acceptable Window list

```json
{"level":"debug","ts":"2021-05-10T05:36:46.617+1000","logger":"advmgr","caller":"sector-storage/sched.go:450","msg":"SCHED Acceptable win: [[] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] [] []]"}
```
## Scheduler Try assign 

```json
{"level":"debug","ts":"2021-05-10T05:36:47.561+1000","logger":"advmgr","caller":"sector-storage/sched.go:465","msg":"SCHED try assign sqi:2 sector 4429 to window 40"}

{"level":"debug","ts":"2021-05-10T05:36:47.561+1000","logger":"advmgr","caller":"sector-storage/sched.go:472","msg":"SCHED ASSIGNED sqi:2 sector 4429 task seal/v0/finalize to window 40"}
```

## Scheduler List Queue and Windows

```json
{"level":"debug","ts":"2021-05-10T05:36:47.561+1000","logger":"advmgr","caller":"sector-storage/sched.go:354","msg":"SCHED 39 queued; 41 open windows"}
```

## Assign Worker Sector

```json
{"level":"debug","ts":"2021-05-10T05:36:48.490+1000","logger":"advmgr","caller":"sector-storage/sched_worker.go:368","msg":"assign worker sector 4429"}
```

## Scheduler Task done message

```json
{"level":"debug","ts":"2021-05-10T05:36:49.011+1000","logger":"advmgr","caller":"sector-storage/sched_worker.go:278","msg":"task done","workerid":"8005744c-ec57-4ad0-9da5-d22e92878e8a"}
```

# Sealing.SectorFinalzeFailed error

```json
{"level":"warn","ts":"2021-05-10T05:36:49.017+1000","logger":"sectors","caller":"storage-sealing/fsm.go:550","msg":"sector 4429 got error event sealing.SectorFinalizeFailed: finalize sector: storage call error 0: allocate local sector for fetching: couldn't find a suitable path for a sector"}

{"level":"info","ts":"2021-05-10T05:36:49.033+1000","logger":"sectors","caller":"storage-sealing/states_failed.go:26","msg":"FinalizeFailed(4429), waiting 59.966777167s before retrying"}
```

# Failed to check worker session error

```json
{"level":"warn","ts":"2021-05-10T07:02:38.527+1000","logger":"advmgr","caller":"sector-storage/sched_worker.go:213","msg":"failed to check worker session","error":"RPC client error: sendRequest failed: Post \"http://10.254.254.203:3456/rpc/v0\": dial tcp 10.254.254.203:3456: connect: connection refused"}
```

