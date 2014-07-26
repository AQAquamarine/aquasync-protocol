###aquasync-protocol

Aquasync protocol drafts / documentations.

Protocol Specification
===

Columns
---

Additional columns added to objects to handle synchronization.

|column name   |type     |presence |origin |description|
|--------------|---------|---------|-------|-----------|
|gid           |GUID     |global   |local  |generated locally when object created|
|UST           |Timestamp|remote   |remote |time when the data get merged to the master|
|deviceToken   |GUID     |global   |local  |notate which device is the origin of the data|
|isDirty       |boolean  |local    |local  |true when modified/created|
|localTimestamp|Timestamp|global   |local  |time when last modified|
|deletedAt     |DateTime |global   |local  |for soft deletion|

And in global context (on local), following variables should be stored.

|column name   |type     |
|--------------|---------|
|latestUST     |Timestamp|
|deviceToken   |GUID     |

#####glossary

- master: Master database (backend)
- UST: Update Sequence Timestamp

Synchronization Sequence
---

1. pullSync (pulls modifications from master database)
2. pushSync (pushes modification which occured on local to master database)

###pullSync

1. Retrieve all resources whose `UST` is newer than `latestUST`
2. For each retrieved resources, find local resource which has same `gid`.
     1. If not exists, create a resource.
     2. If exists, go to `resolveConflict`
3. Set newest `UST` which is found in retrieved resource to `latestUST`.

###pushSync

1. Find all local resources whose `dirty` is `true`.
2. Pack them into one request and send it to the backend.
3. (Backend) Update/Create requested resources.
     1. Perform update when `localTimestamp` is newer than current `localTimestamp`.
     2. Set `UST` current timestamp.

References
--

[Evernote Synchronization via EDAM](https://dev.evernote.com/media/pdf/edam-sync.pdf)
