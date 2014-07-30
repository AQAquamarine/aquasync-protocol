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

Test
---

```rb
describe Book do
  let(:book) { Book.new }

  context "#before_save" do
    before(:each) do
      book.gid = "550E8400-E29B-41D4-A716-446655440000"
      book.save
    end
    it("ust are set when saved") {
      expect(book.ust).to_not eq nil
    }

    it("gid should be lowercase") {
      expect(book.gid).to eq "550e8400-e29b-41d4-a716-446655440000"
    }
  end

  context "validation" do
    before(:each) do
      book.deviceToken = "dddddddd-e29b-41d4-a716-446655dd0000"
      book.gid = "550e8400-e29b-41d4-a716-446655440000"
      book.localTimestamp = 1234567789
    end

    it("does not allow nil for :gid") {
      book.gid = nil
      expect(book.valid?).to eq false
    }

    it("does not allow nil for :deviceToken") {
      book.deviceToken = nil
      expect(book.valid?).to eq false
    }

    it("does not allow nil for :localTimestamp") {
      book.localTimestamp = nil
      expect(book.valid?).to eq false
    }
    it("allows lowercase UUID values for :gid") {
      book.gid = "550e8400-e29b-41d4-a716-446655440000"
      expect(book.valid?).to eq true
    }
    it("does not allow not-UUID value for :gid") {
      book.gid = "hugahuga"
      expect(book.valid?).to eq false
    }
    it("allows lowercase UUID values for :deviceToken") {
      book.deviceToken = "550e8400-e29b-41d4-a716-446655440000"
      expect(book.valid?).to eq true
    }
    it("does not allow not-UUID value for :deviceToken") {
      book.deviceToken = "hugahuga"
      expect(book.valid?).to eq false
    }
  end
end
```
