# ClipDrop Management - AWS Setup Guide

## Overview
This guide sets up automatic cleanup of expired ClipDrop rooms and adds a management interface to the CaffnCode admin portal.

## New Lambda Functions

### 1. clipdrop-cleanup.js
**Purpose:** Auto-delete expired rooms and their S3 contents
**Configuration:** 
- Trigger: EventBridge (CloudWatch Events) - Daily schedule
- Environment Variables: `REGION`, `DYNAMODB_TABLE`, `BUCKET_NAME`
- IAM Permissions: DynamoDB Read/Scan/Delete, S3 List/Delete

**Setup:**
1. Deploy as Lambda function
2. **[→ See EVENTBRIDGE_SETUP.md for full EventBridge configuration](EVENTBRIDGE_SETUP.md)**
3. Quick reference:
   - Rule name: `clipdrop-cleanup-daily`
   - Cron: `0 2 * * ? *` (2 AM UTC daily)
   - Target: clipdrop-cleanup Lambda

---

### 2. admin-clipdrop-list-rooms.js
**Purpose:** List all active ClipDrop rooms with statistics
**Endpoint:** GET `/admin/clipdrop/rooms`
**Authentication:** Required (Cognito token)
**Environment Variables:** `REGION`, `DYNAMODB_TABLE`, `ADMINS_TABLE`, `BUCKET_NAME`
**Returns:** Array of rooms with stats (count files, size, expiry)

---

### 3. admin-clipdrop-delete-room.js
**Purpose:** Delete a specific room and ALL its S3 contents
**Endpoint:** POST `/admin/clipdrop/rooms/{code}/delete`
**Authentication:** Required (Cognito token)
**Environment Variables:** `REGION`, `DYNAMODB_TABLE`, `BUCKET_NAME`
**Deletes:**
- All S3 objects in `rooms/{CODE}/` prefix
- DynamoDB room record

---

### 4. admin-clipdrop-delete-file.js
**Purpose:** Delete a single file from a room
**Endpoint:** POST `/admin/clipdrop/rooms/{code}/files/{filename}/delete`
**Authentication:** Required (Cognito token)
**Environment Variables:** `REGION`, `DYNAMODB_TABLE`, `BUCKET_NAME`
**Deletes:**
- Specific S3 object
- File record from DynamoDB room's files array

---

### 5. admin-clipdrop-delete-room-contents.js
**Purpose:** Delete all files from a room (keep room active)
**Endpoint:** POST `/admin/clipdrop/rooms/{code}/clear`
**Authentication:** Required (Cognito token)
**Environment Variables:** `REGION`, `DYNAMODB_TABLE`, `BUCKET_NAME`
**Deletes:**
- All S3 objects in `rooms/{CODE}/` prefix
- Clears files array in DynamoDB (room remains)

---

## Required Environment Variables

### Cleanup Function (Scheduled)
**clipdrop-cleanup.js**
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
BUCKET_NAME=clipdrop-caffncode
```

### Admin Functions (Authentication Required)
**admin-clipdrop-list-rooms.js**
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

**admin-clipdrop-delete-room.js**
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

**admin-clipdrop-delete-file.js**
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

**admin-clipdrop-delete-room-contents.js**
```
REGION=eu-north-1
DYNAMODB_TABLE=clipdrop-rooms
ADMINS_TABLE=caffncode-admins
BUCKET_NAME=clipdrop-caffncode
```

**Note:** All admin functions require Cognito authentication. The ClipDrop Lambda functions use a separate `BUCKET_NAME` (clipdrop-caffncode) from the admin assets bucket (caffncode-assets).

---

## API Gateway Routes

Add these routes to your API Gateway (or update existing admin API):

```
POST   /admin/clipdrop/rooms              → admin-clipdrop-list-rooms
GET    /admin/clipdrop/rooms              → admin-clipdrop-list-rooms
POST   /admin/clipdrop/rooms/{code}/delete         → admin-clipdrop-delete-room
DELETE /admin/clipdrop/rooms/{code}/delete         → admin-clips3
POST   /admin/clipdrop/rooms/{code}/clear          → admin-clipdrop-delete-room-contents
POST   /admin/clipdrop/rooms/{code}/files/{filename}/delete  → admin-clipdrop-delete-file
POST   /admin/clipdrop/cleanup            → clipdrop-cleanup (optional manual trigger)
```

---

## Admin Portal Updates

✅ **Already Done:**
- Added "ClipDrop" to sidebar navigation
- Added ClipDrop management section with:
  - Room statistics (total rooms, files, storage)
  - List all active rooms showing:
    - Room code and name
    - Status (Active/Expired)
    - File count and total size
    - Max size limit
    - Time until expiry
  - Actions per room:
    - View files (modal)
    - Clear contents
    - Delete room
  - Delete individual files from room
  - Delete all expired rooms (cleanup)

---

## S3 Bucket Configuration

**Bucket:** `clipdrop-caffncode`

Ensure:
1. **Versioning:** Optional (cleanup will delete all versions)
2. **Lifecycle:** Consider setting expiration policy as backup
3. **Bucket Policy:** Lambda function IAM role has permissions:
```json
{
  "Effect": "Allow",
  "Action": [
    "s3:ListBucket",
    "s3:GetObject",
    "s3:DeleteObject",
    "s3:ListBucketVersions",
    "s3:DeleteObjectVersion"
  ],
  "Resource": [
    "arn:aws:s3:::caffncode-clipdrop/*",
    "arn:aws:s3:::caffncode-clipdrop"
  ]
}
```

---

## Testing

1. **List Rooms:**
   ```
   curl -H "Authorization: Bearer {TOKEN}" \
     https://ut04js0exa.execute-api.eu-north-1.amazonaws.com/admin/clipdrop/rooms
   ```

2. **Delete Room:**
   ```
   curl -X POST -H "Authorization: Bearer {TOKEN}" \
     https://ut04js0exa.execute-api.eu-north-1.amazonaws.com/admin/clipdrop/rooms/CAFF-1234/delete
   ```

3. **Clear Room Contents:**
   ```
   curl -X POST -H "Authorization: Bearer {TOKEN}" \
     https://ut04js0exa.execute-api.eu-north-1.amazonaws.com/admin/clipdrop/rooms/CAFF-1234/clear
   ```

---

## Monitoring

Lambda CloudWatch Logs to monitor:
- `clipdrop-cleanup` - Daily cleanup job
- `admin-clipdrop-list-rooms` - Admin viewing rooms
- `admin-clipdrop-delete-room` - Admin deleting rooms
- Admin console error messages

---

## Quick Deployment Checklist

- [ ] Deploy **clipdrop-cleanup.js**
  - Set env vars: `REGION`, `DYNAMODB_TABLE`, `BUCKET_NAME`
  - Create EventBridge rule with cron schedule: `cron(0 2 * * ? *)` (2 AM UTC daily)

- [ ] Deploy **admin-clipdrop-list-rooms.js**
  - Set env vars: `REGION`, `DYNAMODB_TABLE`, `ADMINS_TABLE`, `BUCKET_NAME`
  - Requires Cognito authentication

- [ ] Deploy **admin-clipdrop-delete-room.js**
  - Set env vars: `REGION`, `DYNAMODB_TABLE`, `ADMINS_TABLE`, `BUCKET_NAME`
  - Requires Cognito authentication

- [ ] Deploy **admin-clipdrop-delete-file.js**
  - Set env vars: `REGION`, `DYNAMODB_TABLE`, `ADMINS_TABLE`, `BUCKET_NAME`
  - Requires Cognito authentication

- [ ] Deploy **admin-clipdrop-delete-room-contents.js**
  - Set env vars: `REGION`, `DYNAMODB_TABLE`, `ADMINS_TABLE`, `BUCKET_NAME`
  - Requires Cognito authentication

- [ ] Add API Gateway routes (see API Routes section above)

- [ ] Update Lambda IAM roles with S3 and DynamoDB permissions

- [ ] Test from Admin Portal: Click "☕ ClipDrop" in sidebar

---

## Notes

- **Immediate Deletion:** All deletions from S3 are immediate (no recovery)
- **Audit Trail:** Consider enabling S3 access logging for deletion tracking
- **Analytics:** Track cleanup job success rates via CloudWatch
- **Future:** Consider DynamoDB Streams for real-time cleanup instead of scheduled Lambda
