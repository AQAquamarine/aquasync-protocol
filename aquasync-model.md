Aquasync Model
---

Aquasync Model should have following columns.

|column name   |type     |example                             |const?  |presence |origin |description|
|--------------|---------|------------------------------------|--------|---------|-------|-----------|
|gid           |lowercase UUIDv1   |550e8400-e29b-41d4-a716-446655dd0000|constant|global   |local  |generated locally when object created|
|UST           |Timestamp|1234567789                          |variable|remote   |remote |time when the data get merged to the master|
|deviceToken   |lowercase UUID     |550e8400-e29b-41d4-a716-446655dd0000|constant|global   |local  |notate which device is the origin of the data|
|isDirty       |BOOL     |true                                |variable|local    |local  |true when modified/created (do not set while synchronization)|
|localTimestamp|Timestamp|1234567789                          |variable|global   |local  |time when last modified (do not set while synchronization)|
|isDeleted     |BOOL     |false                               |variable|global   |local  |for soft deletion|

- **`gid` SHOULD BE LOWERCASE.**
- **`deviceToken` SHOULD BE LOWERCASE.**

The test written in RSpec will be provided.
