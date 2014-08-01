ErrorCodes
---

- 200: Success
- 401: Unauthorized
- 422: Validation Error

### 200 Success

Should serve following body.

```json
{
  "id": "success"
}
```

### 422 Validation Error

Should serve following body.

```json
{
  "id": "validation_error",
  "message": "SOME_MESSAGE_FOR_DESCRIBE_ERROR",
  "url": "https://github.com/AQAquamarine/aquasync-protocol/blob/master/aquasync-model.md#validations"
}
```
