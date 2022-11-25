1. clone powergate project

`git clone https://github.com/textileio/powergate.git`

2. cd to docker folder in *powergate*

`cd powergate/docker`

3. run localnet (required installing docker and docker-compose)

`make localnet`

4. after build and run powergate create new user with *pow* cli tools

`pow admin users create`

```json
{
  "user": {
    "id": "a0380d38-f364-40bc-bd60-f379cfe63383",
    "token": "3115eaff-7af5-4c6c-a3d6-c2f5838c5a00"
  }
}
```

5. export powergate token to env

`export POW_TOKEN=3115eaff-7af5-4c6c-a3d6-c2f5838c5a00`

6. get wallet address

`pow wallet addrs` 

```json
{
  "addresses": [
    {
      "name": "Initial Address",
      "address": "f3wpdiddr2fjd5hpbpig2eiwtbckgh2wkqfwcamf5wdwoqlkodlld6yt6fzrhosqjieaoo6gyqfzivtetxw3gq",
      "type": "bls",
      "balance": "250000000000000000",
      "verifiedClientInfo": null
    }
  ]
}
```

7. create default config powergate

`pow config default > default-config`

8. create testfile test for stage 

`touch testfile`

9. send to stage *testfile*

`pow data stage testfile`

```json
{
  "cid": "QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov"
}
```

10. watch applied config for cid

`pow config apply --watch QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov`

```console
                 JOB ID                |       STATUS       | MINER  | PRICE |    DEAL STATUS     
---------------------------------------+--------------------+--------+-------+--------------------
  f5016575-b442-4594-89f6-2246437562f3 | JOB_STATUS_SUCCESS |        |       |                    
                                       |                    | f01000 | 15258 | StorageDealActive  
```

11. now retrieved *testfile* 

`pow data get QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov ret-testfile`

```console
> Success! Data written to ret-testfile
```

12. now check retrieved file is okay

`vi ret-testfile`

13. disable *hot* in `default-config` file

`nano default-config`

```json
{
  "hot": {
    "enabled": false,
    "allowUnfreeze": false,
    "unfreezeMaxPrice": "0",
    "ipfs": {
      "addTimeout": "10"
    }
  },
...
```

14. apply new config for old cid

`pow config apply -c default-config -o --watch QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov`

```console
                 JOB ID                |       STATUS       | MINER | PRICE | DEAL STATUS  
---------------------------------------+--------------------+-------+-------+--------------
  c33ad3e5-6832-4e8d-ade0-c8991f9e376e | JOB_STATUS_SUCCESS |       |       |              
```

15. see pinned all files cid

`docker exec localnet_ipfs_1 ipfs pin ls`

```console
QmfERRsJqfLTSmpdPi7hAWdrVPujytCorKFLqR2fBoXHUo recursive
QmPZ9gcCEpqKTo6aq61g2nXGUhM4iCL3ewB6LDXZCtioEB indirect
QmQ5vhrL7uv6tuoN9KeVBwd4PwfQkXdVVmDLUZuTNxqgvm indirect
QmQGiYLVAdSHJQKYFRTJZMG4BXBHqKperaZtyKGmCRLmsF indirect
QmQPeNsJPyVWPFDVHb77w8G42Fvo15z4bG2X8D2GhfbSXc recursive
QmU5k7ter3RdjZXu3sHghsga1UQtrztnQxmTL22nPnsu3g indirect
QmQy6xmJhrcC5QLboAcGFcAE1tC8CrwDVkrHdEYJkLscrQ indirect
QmUNLLsPACCz1vLxQVkXqqLX5R1X345qqfHbsf67hvA3Nn recursive
QmYCvbfNbCwFR45HiNP45rwJgvatpiW38D961L5qAhUM5Y indirect
QmejvEPop4D7YUadeGqYWmZxHhLc4JBUCzJJHWMzdcMe2y indirect
```

16. remove all pinned repos

`docker exec localnet_ipfs_1 ipfs repo gc`

```console
removed QmUBk3ibLaDaLz2FU15VkRfgbwsdY3GVp2NFWjiwswQ2es
removed QmPJWgZumK11DquASVuKkG55XXEPxWZ62a1UVBoCHxgEnM
removed QmfKAim9948xHwqN5N67zPsYF83qpYrbUZsJeEnKpagxFM
removed QmUzeVpcxnJjamyo2EV15ntmy9JGRALFRknHZEAVhC5Jtz
removed QmUqKhVJCtjj7KXqf4AKNjQxW2krN34vFz6uMV4vPDMSoe
removed QmS9SJXApfi59hqD8Naktgakc6UNHK7XDhqhtMg9sBhY8g
removed QmeeAzS2zaJL1WTqAnfpJKJdLcxWHgzEHEBgdnuMJLyMFk
removed QmdNwKXhY2DdGscUX9sc5gBtbXiepmiFd4cArFQFphrHZ4
removed QmUpYtJoyby5jYg9oez8qLpMxqae4pe6rHxsmtWLH2APTZ
removed QmbP17p1iD3QQhVihRHfkgWvUjeZuXV14Cfi14piyxneTx
removed Qmd3Br6JqjUdVCRLRK6EF9RwACBJBMamidbXJy1mRHAiWe
removed QmZ1FHWZ7H21gd9psUjbsMq553Z6JaD7KdRQr6deYzCdKP
removed QmZgAaSyubvjpLKpr8rw328smaTs6VduWPw7xscDVGLcDr
removed Qmb9Mbk9Z5p72pSsgFQ1wMpm1fZScQqxy6rG5qwE23GGW7
removed QmWRREU4Zt6Y7jnimD2cLrsTmCEXAAnowNKfBM8PLzo4PV
removed QmRGXm9gpLBkUsz7SBDNgeR45vMK3w99R5Ep6JGAYjuwqy
removed QmW4muv7TdWf88QGky278wfLdM2Xr6EEpUmhBGydehxKMZ
removed QmNsVthMF6TuEA6Ybh33f5RVQDCvd35dvD1ehPL2KUAs4Z
removed QmZVgTkHu3735MY5PxPFNgRsrgpyVJtSaXxWKnV3fxz6xH
removed QmStKMwJpbtAuoSkgAbGRsRQnbrtonZxZCPa2mYii1oCGa
removed QmSYQFVAZgEnpo6NxiW5agyj3XU9VR4CbERShXiLhuPPPE
removed QmPZhyTu8D7NqR5NvgkgNYsSYD4CNjnyuFejB4i23itJvA
```

17. now we enable hot stage and allow unfreeze cid

`nano default-config`

```json
{
  "hot": {
    "enabled": true,
    "allowUnfreeze": true,
    "unfreezeMaxPrice": "0",
    "ipfs": {
      "addTimeout": "10"
    }
  },
...
```

18. now again we transfer cid to hot stage 

`pow config apply -c default-config -o --watch QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov`

```console
                 JOB ID                |       STATUS       | MINER | PRICE | DEAL STATUS  
---------------------------------------+--------------------+-------+-------+--------------
  ec189a9c-26c3-45be-8392-e3d3f0a2a920 | JOB_STATUS_SUCCESS |       |       |              
```

19. again retrieved file from stage

`pow data get QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov ret-testfile`

```console
> Success! Data written to ret-testfile
```

20. now we can see pinned again

`docker exec localnet_ipfs_1 ipfs pin ls`

```console
QmPZ9gcCEpqKTo6aq61g2nXGUhM4iCL3ewB6LDXZCtioEB indirect
QmQPeNsJPyVWPFDVHb77w8G42Fvo15z4bG2X8D2GhfbSXc recursive
QmU5k7ter3RdjZXu3sHghsga1UQtrztnQxmTL22nPnsu3g indirect
QmfERRsJqfLTSmpdPi7hAWdrVPujytCorKFLqR2fBoXHUo recursive
QmQ5vhrL7uv6tuoN9KeVBwd4PwfQkXdVVmDLUZuTNxqgvm indirect
QmQGiYLVAdSHJQKYFRTJZMG4BXBHqKperaZtyKGmCRLmsF indirect
QmQy6xmJhrcC5QLboAcGFcAE1tC8CrwDVkrHdEYJkLscrQ indirect
QmStKMwJpbtAuoSkgAbGRsRQnbrtonZxZCPa2mYii1oCGa recursive
QmUNLLsPACCz1vLxQVkXqqLX5R1X345qqfHbsf67hvA3Nn recursive
QmYCvbfNbCwFR45HiNP45rwJgvatpiW38D961L5qAhUM5Y indirect
QmejvEPop4D7YUadeGqYWmZxHhLc4JBUCzJJHWMzdcMe2y indirect
```

21. with use switch log we can see events

`pow data log QmVW7GKxtnL53G7XUuw2DE22LHaXXnH3WbE4JUT46aeDov`

```console
> 2021-05-10T10:35:53 - Pushing new configuration...
> 2021-05-10T10:35:53 - Configuration saved successfully
> 2021-05-10T10:35:53 - Executing job f5016575-b442-4594-89f6-2246437562f3...
> 2021-05-10T10:35:53 - Executing Hot-Storage configuration...
> 2021-05-10T10:35:53 - Fetching from the IPFS network...
> 2021-05-10T10:35:53 - Hot-Storage configuration ran successfully.
> 2021-05-10T10:35:53 - Executing Cold-Storage configuration...
> 2021-05-10T10:35:53 - Current replication factor is lower than desired, making 1 new deals...
> 2021-05-10T10:35:53 - Entering deal preprocessing queue...
> 2021-05-10T10:35:53 - Calculating piece size...
> 2021-05-10T10:35:53 - The payload size is 30 KiB, and the calculated piece size is 32 KiB
> 2021-05-10T10:35:53 - Proposing deal to miner f01000 with 0.0000000005 FIL per epoch...
> 2021-05-10T10:35:53 - Watching deals unfold...
> 2021-05-10T10:35:53 - Deal with miner f01000 changed state to StorageDealReserveClientFunds
> 2021-05-10T10:35:54 - Deal with miner f01000 changed state to StorageDealClientFunding
> 2021-05-10T10:35:55 - Deal with miner f01000 changed state to StorageDealStartDataTransfer
> 2021-05-10T10:35:55 - Deal with miner f01000 changed state to StorageDealTransferring
> 2021-05-10T10:35:56 - Deal with miner f01000 changed state to StorageDealCheckForAcceptance
> 2021-05-10T10:35:59 - Deal with miner f01000 changed state to StorageDealProposalAccepted
> 2021-05-10T10:36:00 - Deal 3 with miner f01000 changed state to StorageDealAwaitingPreCommit
> 2021-05-10T10:36:25 - Deal 3 with miner f01000 changed state to StorageDealSealing
> 2021-05-10T10:37:12 - Deal 3 with miner f01000 is active on-chain
> 2021-05-10T10:37:12 - Cold-Storage configuration ran successfully.
> 2021-05-10T10:37:12 - Job f5016575-b442-4594-89f6-2246437562f3 execution finished with status Success.
> 2021-05-10T10:46:25 - Pushing new configuration...
> 2021-05-10T10:46:25 - Configuration saved successfully
> 2021-05-10T10:46:25 - Executing job c33ad3e5-6832-4e8d-ade0-c8991f9e376e...
> 2021-05-10T10:46:25 - Automatically staging Cid from the IPFS network...
> 2021-05-10T10:46:25 - Executing Cold-Storage configuration...
> 2021-05-10T10:46:25 - The current replication factor is equal or higher than desired, avoiding making new deals.
> 2021-05-10T10:46:25 - Cold-Storage configuration ran successfully.
> 2021-05-10T10:46:25 - Executing Hot-Storage configuration...
> 2021-05-10T10:46:25 - Data was unpinned.
> 2021-05-10T10:46:25 - Hot-Storage configuration ran successfully.
> 2021-05-10T10:46:25 - Job c33ad3e5-6832-4e8d-ade0-c8991f9e376e execution finished with status Success.
> 2021-05-10T10:54:49 - Pushing new configuration...
> 2021-05-10T10:54:49 - Configuration saved successfully
> 2021-05-10T10:54:49 - Executing job ec189a9c-26c3-45be-8392-e3d3f0a2a920...
> 2021-05-10T10:54:49 - Executing Hot-Storage configuration...
> 2021-05-10T10:54:49 - Fetching from the IPFS network...
> 2021-05-10T10:54:59 - Direct fetching from IPFS wasn't possible.
> 2021-05-10T10:54:59 - Unfreezing from Filecoin...
> 2021-05-10T10:54:59 - Fetching from f01000...
> 2021-05-10T10:54:59 - Received 0 B, total spent: 0FIL (ClientEventOpen/DealStatusNew)
> 2021-05-10T10:54:59 - Received 0 B, total spent: 0FIL (ClientEventDealProposed/DealStatusWaitForAcceptance)
> 2021-05-10T10:54:59 - Received 0 B, total spent: 0FIL (ClientEventDealAccepted/DealStatusAccepted)
> 2021-05-10T10:54:59 - Received 0 B, total spent: 0FIL (ClientEventLastPaymentRequested/DealStatusAccepted)
> 2021-05-10T10:54:59 - Received 30 KiB, total spent: 0FIL (ClientEventBlocksReceived/DealStatusAccepted)
> 2021-05-10T10:54:59 - Received 30 KiB, total spent: 0FIL (ClientEventAllBlocksReceived/DealStatusAccepted)
> 2021-05-10T10:54:59 - Received 30 KiB, total spent: 0FIL (ClientEventPaymentChannelCreateInitiated/DealStatusPaymentChannelCreating)
> 2021-05-10T10:55:01 - Received 30 KiB, total spent: 0FIL (ClientEventPaymentChannelReady/DealStatusPaymentChannelAllocatingLane)
> 2021-05-10T10:55:01 - Received 30 KiB, total spent: 0FIL (ClientEventLaneAllocated/DealStatusOngoing)
> 2021-05-10T10:55:01 - Received 30 KiB, total spent: 0FIL (ClientEventLastPaymentRequested/DealStatusFundsNeededLastPayment)
> 2021-05-10T10:55:01 - Received 30 KiB, total spent: 0FIL (ClientEventSendFunds/DealStatusSendFundsLastPayment)
> 2021-05-10T10:55:01 - Received 30 KiB, total spent: 0.000000000000061468FIL (ClientEventPaymentSent/DealStatusFinalizing)
> 2021-05-10T10:55:02 - Received 30 KiB, total spent: 0.000000000000061468FIL (ClientEventComplete/DealStatusCompleted)
> 2021-05-10T10:55:02 - Unfrozen successfully from f01000 with cost 61468 attoFil, saving in Hot-Storage...
> 2021-05-10T10:55:02 - Hot-Storage configuration ran successfully.
> 2021-05-10T10:55:02 - Executing Cold-Storage configuration...
> 2021-05-10T10:55:02 - The current replication factor is equal or higher than desired, avoiding making new deals.
> 2021-05-10T10:55:02 - Cold-Storage configuration ran successfully.
> 2021-05-10T10:55:02 - Job ec189a9c-26c3-45be-8392-e3d3f0a2a920 execution finished with status Success.
```
