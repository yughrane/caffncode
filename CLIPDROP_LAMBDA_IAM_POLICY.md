# ClipDrop Lambda - Comprehensive IAM Policy

## All-in-One Policy for `clipdrop-lambda-role`

Use this single policy for your `clipdrop-lambda-role`. It includes all permissions needed for:
- ✅ clipdrop-cleanup.js
- ✅ create-room.js
- ✅ get-room.js
- ✅ presign-upload.js
- ✅ presign-download.js
- ✅ admin-clipdrop-list-rooms.js
- ✅ admin-clipdrop-delete-room.js
- ✅ admin-clipdrop-delete-file.js
- ✅ admin-clipdrop-delete-room-contents.js

---

## Replace/Add This Policy

Go to **IAM → Roles → clipdrop-lambda-role** and replace your policy with:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DynamoDBAll",
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Scan",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:eu-north-1:707002484299:table/clipdrop-rooms"
    },
    {
      "Sid": "S3All",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:ListBucketVersions",
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:DeleteObjectVersion"
      ],
      "Resource": [
        "arn:aws:s3:::clipdrop-caffncode",
        "arn:aws:s3:::clipdrop-caffncode/*"
      ]
    }
  ]
}
```

---

## What This Includes

### DynamoDB Permissions:
- `PutItem` - Create rooms (create-room.js)
- `GetItem` - Fetch room details (get-room.js, presign-upload.js, presign-download.js, admin functions)
- `UpdateItem` - Update room metadata (presign-upload.js, admin-clipdrop-delete-file.js)
- `DeleteItem` - Delete rooms (clipdrop-cleanup.js, admin-clipdrop-delete-room.js)
- `Scan` - List all rooms (clipdrop-cleanup.js, admin-clipdrop-list-rooms.js)
- `Query` - Query rooms by attributes (reserved for future use)

### S3 Permissions:
- `ListBucket` - List files in room (clipdrop-cleanup.js, admin delete functions)
- `ListBucketVersions` - Handle versioned objects
- `GetObject` - Download files (presign-download.js)
- `PutObject` - Upload files (presign-upload.js)
- `DeleteObject` - Delete files (clipdrop-cleanup.js, admin functions)
- `DeleteObjectVersion` - Delete versioned objects

---

## How to Apply

1. Go to **AWS Console → IAM → Roles**
2. Click **clipdrop-lambda-role**
3. Click **Permissions** tab
4. Find the existing policy (or click **Add inline policy**)
5. Replace the JSON with the policy above
6. Click **Review and save**

Done! All Lambdas should now work. ✅
