# Lambda Environment Variables - Quick Reference

## ClipDrop Management Functions

### 1. clipdrop-cleanup.js
**Type:** Scheduled (EventBridge daily trigger)
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
BUCKET_NAME=clipdrop-caffncode
```

### 2. admin-clipdrop-list-rooms.js
**Type:** Admin API (requires Cognito token)
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

### 3. admin-clipdrop-delete-room.js
**Type:** Admin API (requires Cognito token)
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

### 4. admin-clipdrop-delete-file.js
**Type:** Admin API (requires Cognito token)
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

### 5. admin-clipdrop-delete-room-contents.js
**Type:** Admin API (requires Cognito token)
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

---

## Notes

- All functions must have `REGION=eu-north-1`
- ClipDrop functions use separate bucket: `clipdrop-caffncode` (not caffncode-assets)
- Admin functions require Cognito token in Authorization header
- Admin functions verify user exists in `caffncode-admins` table (security check)
- Scheduled cleanup function runs daily at 2 AM UTC
