ErrorCodes
---

- 200: Success
- 204: No Content
- 401: Unauthorized
- 422: Validation Error

### 200 Success

Should serve following body.

```json
{
  "id": "success"
}
```

### 204 No Conent

If there is no updated content, HTTP Status Code should be `204` and serve nothing in body.

### 422 Validation Error

Should serve following body.

```json
{
  "id": "validation_error",
  "message": "SOME_MESSAGE_FOR_DESCRIBE_ERROR",
  "url": "https://github.com/AQAquamarine/aquasync-protocol/blob/master/aquasync-model.md#validations"
}
```
