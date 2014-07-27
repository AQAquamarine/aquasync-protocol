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

Every DeltaPack should have `id` to prevent duplicated maniplation and these `id` should be stored in every devices / backend datastore.

```json
{
  "id": "0f72fa94-d0e3-497c-8528-af25df5ff7c9" // UUID
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

Backend Endpoint
---

The backend should respond to `POST /deltas` for push and `GET /deltas/from::ust` for pull.

### POST /deltas

Should receive `DeltaPack` data and return the status in HTTP Code. (like `200 for Success`)

### GET /deltas/from::ust

example: `GET /deltas/from:123456789`

Should return `DeltaPack` data with appropriate `Content-Type`. (like `application/json`)

####Concerns

- Too big `DeltaPack`? Separete them into several DeltaPacks?
- How about pushing one delta? How can I send an object efficiently?

Synchronization Sequence
---

1. #pullSync (pulls modifications from master database)
2. #pushSync (pushes modification which occured on local to master database)

### \#pullSync

1. Retrieve all resources whose `UST` is newer than `device.latestUST` and `deviceToken` defers from `device.deviceToken`.
2. For each retrieved resources, find local resource which has same `gid`.
     1. If not exists, create a resource.
     2. If exists, update the record if `isDirty` is not `true`. If `true`, go to `#resolveConflice`.
     3. **DO NOT UPDATE `isDirty` AND `localTimestamp` WHILE `#pullSync`. IT WILL OCCUR DATA DUPLICATION.**
3. Set newest `UST` which is found in retrieved resource to `latestUST`.

### \#pushSync

1. Find all local resources whose `dirty` is `true`.
2. Pack them into one request and send it to the backend.
3. (Backend) Update/Create requested resources.
     1. Perform update when `localTimestamp` is newer than current `localTimestamp`.
     2. Set `UST` current timestamp.

#### \#resolveConflict

1. See `localTimestamp`. Newer one should be saved.
 
Timing
---

`#sync` should occur when..

1. When any object is modified in local.
2. When any object is modified in any device and get merged on master.
3. When the device get network connection.

References
--

[Evernote Synchronization via EDAM](https://dev.evernote.com/media/pdf/edam-sync.pdf)

Related links
---

- [Aquasync](https://github.com/AQAquamarine/Aquasync) - Official implementation for iOS

LICENSE
---

The MIT License (MIT)

Copyright (c) 2014 kaiinui

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
