###DeltaPack

To pull/push deltas from/to the backend, following data structure are used. Although example data is represent as JSON, Aquasync does not depend on JSON. Data can be given by JSON, MessagePack or plist.

Every DeltaPack should have `_id` to prevent duplicated maniplation and these `_id` should be stored in every devices / backend datastore.

```json
{
  "_id": "0f72fa94-d0e3-497c-8528-af25df5ff7c9" // UUID
  "Book": [
    {
      "column_1": "data_1",
      "column_2": "data_2"
    },
    {..},
    ..
  ],
  "Author": [
    {
      "column_1": "data_1"
    },
    {..},
    ..
  ],
  ..
}
```

- **CLASS NAME SHOULD BE PASCAL CASE (e.g. `Book`).**
- Other columns are not ristricted.

Structure
---

1. (root)
  1. _id = lowercase UUID
  2. {MODEL_NAME} = Array<[Delta](https://github.com/AQAquamarine/aquasync-protocol/blob/master/delta.md)>

Example
---

|Book                                |name               |author_name   |
|------------------------------------|-------------------|--------------|
|0f72fa94-d0e3-497c-8528-af25df5ff7c9|The Little Prince  |Taro Tanaka   |
|1f72fa94-d0e3-497c-8528-af25df5ff7c9|Alice in Wonderland|Bob Dylan     |
|2f72fa94-d0e3-497c-8528-af25df5ff7c9|Harry Potter       |Charles Schwab|

|Author                              |name          |
|------------------------------------|--------------|
|2c72fa94-d0e3-497c-8528-af25df5ff7c9|Taro Tanaka   |

#####DeltaPack

```json
{
  "_id": "0f72fa94-dae3-4d3c-8528-af25df5ff7c9",
  "Book": [
    {
      "id": "0f72fa94-d0e3-497c-8528-af25df5ff7c9",
      "name": "The Little Prince",
      "author_name": "Taro Tanaka"
    },
    {
      "id": "1f72fa94-d0e3-497c-8528-af25df5ff7c9",
      "name": "Alice in Wonderland",
      "author_name": "Bob Dylan"
    },
    {
      "id": "2f72fa94-d0e3-497c-8528-af25df5ff7c9",
      "name": "Harry Potter",
      "author_name": "Charles Schwab"
    }
  ],
  "Author": [
    {
      "id": "2c72fa94-d0e3-497c-8528-af25df5ff7c9",
      "name": "Taro Tanaka"
    }
  ]
}
```
