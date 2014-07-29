###DeltaPack

To pull/push deltas from/to the backend, following data structure are used. Although example data is represent as JSON, Aquasync does not depend on JSON. Data can be given by JSON, MessagePack or plist.

Every DeltaPack should have `id` to prevent duplicated maniplation and these `id` should be stored in every devices / backend datastore.

```json
{
  "_id": "0f72fa94-d0e3-497c-8528-af25df5ff7c9" // UUID
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
