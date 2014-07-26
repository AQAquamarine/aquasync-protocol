aquasync-protocol
=================

Aquasync protocol drafts / documentations.

Protocol
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

- master: Master database (backend)
- UST: Update Sequence Timestamp

Synchronization Sequence
---

1. pullSync (pulls modifications from master database)
2. pushSync (pushes modification which occured on local to master database)
