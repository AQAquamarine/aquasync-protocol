![Aquasync](https://dl.dropboxusercontent.com/u/7817937/_github/aquamarine/aquasync_logo.png)

Aquasync Protocol Specification
===

Columns
---

Additional columns added to objects to handle synchronization.

|column name   |type     |const?  |presence |origin |description|
|--------------|---------|--------|---------|-------|-----------|
|gid           |GUID     |constant|global   |local  |generated locally when object created|
|UST           |Timestamp|variable|remote   |remote |time when the data get merged to the master|
|deviceToken   |GUID     |constant|global   |local  |notate which device is the origin of the data|
|isDirty       |boolean  |variable|local    |local  |true when modified/created (do not set while synchronization)|
|localTimestamp|Timestamp|variable|global   |local  |time when last modified (do not set while synchronization)|
|deletedAt     |DateTime |variable|global   |local  |for soft deletion|

And in global context (on local), following variables should be stored.

|column name   |type     |
|--------------|---------|
|latestUST     |Timestamp|
|deviceToken   |GUID     |

For convinience, call them like `device.latestUST` in this specification.

#####glossary

- master: Master database (backend)
- UST: Update Sequence Timestamp

Data Structure
---

###DeltaPack

To pull/push deltas from/to the backend, following data structure are used. Although example data is expressed as JSON, Aquasync does not depend on JSON. It is data expression agnostic. Data can be expressed as JSON/MessagePack/plist.

```json
{
  "model1_name": [
    {
      "column_1": "data_1",
      "column_2": "data_2"
    },
    {..},
    ..
  ],
  "model2_name": [
    {
      "column_1": "data_1"
    },
    {..},
    ..
  ],
  ..
}
```

Synchronization Sequence
---

1. #pullSync (pulls modifications from master database)
2. #pushSync (pushes modification which occured on local to master database)

### \#pullSync

1. Retrieve all resources whose `UST` is newer than `device.latestUST` and `deviceToken` defers from `device.deviceToken`.
2. For each retrieved resources, find local resource which has same `gid`.
     1. If not exists, create a resource.
     2. If exists, update the record if `isDirty` is not `true`. If `true`, go to `#resolveConflice`.
     3. **DO NOT UPDATE `isDirty` and `localTimestamp` while `#pullSync`.**
3. Set newest `UST` which is found in retrieved resource to `latestUST`.

### \#pushSync

1. Find all local resources whose `dirty` is `true`.
2. Pack them into one request and send it to the backend.
3. (Backend) Update/Create requested resources.
     1. Perform update when `localTimestamp` is newer than current `localTimestamp`.
     2. Set `UST` current timestamp.

#### \#resolveConflict

1. If `A.localTimestamp` is newer than `B.localTimestamp`, update the record.

References
--

[Evernote Synchronization via EDAM](https://dev.evernote.com/media/pdf/edam-sync.pdf)

Related links
---

- [Aquasync](https://github.com/AQAquamarine/Aquasync) - Official implementation for iOS
