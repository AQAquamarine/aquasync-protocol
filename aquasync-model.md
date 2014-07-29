Aquasync Model
---

Aquasync Model should have following columns.

|column name   |type     |const?  |presence |origin |description|
|--------------|---------|--------|---------|-------|-----------|
|gid           |UUIDv1   |constant|global   |local  |generated locally when object created|
|UST           |Timestamp|variable|remote   |remote |time when the data get merged to the master|
|deviceToken   |GUID     |constant|global   |local  |notate which device is the origin of the data|
|isDirty       |BOOL     |variable|local    |local  |true when modified/created (do not set while synchronization)|
|localTimestamp|Timestamp|variable|global   |local  |time when last modified (do not set while synchronization)|
|isDeleted     |BOOL     |variable|global   |local  |for soft deletion|
