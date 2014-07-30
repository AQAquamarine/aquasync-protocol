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

Validations
---

```rb
validates_presence_of :gid
validates_format_of :gid, with: /[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}/
validates_presence_of :ust
validates_presence_of :localTimestamp
validates_presence_of :deviceToken
validates_format_of :deviceToken, with: /[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}/
```

Callbacks
---

```rb
before_validation do
  downcase_gid
  downcase_device_token
  set_ust
end

def downcase_gid
  self.gid.try(:downcase!)
end

def downcase_device_token
  self.deviceToken.try(:downcase!)
end

def set_ust
  self.ust = Time.now.to_i
end
```
