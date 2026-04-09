# S3 Admin Photo Upload - Setup Checklist

## Required AWS Lambda Environment Variables

Make sure these are set on the `admin-photo-upload` Lambda function:

```
REGION=eu-north-1
BUCKET_NAME=caffncode-assets
ADMINS_TABLE=caffncode-admins
ASSETS_CDN_URL=https://assets.caffncode.com    # (optional, defaults to this)
```

**All Lambda functions require:**
- REGION=eu-north-1
- COGNITO_USER_POOL_ID=eu-north-1_hTVJrQ2rI
- COGNITO_CLIENT_ID=9je3d0m0ftm1iq640mvok6l34
- ADMINS_TABLE=caffncode-admins
- NOTICES_TABLE=caffncode-notices
- BUCKET_NAME=caffncode-assets

## S3 Bucket CORS Configuration

The S3 bucket (`caffncode-assets`) must have CORS enabled to allow direct uploads from the browser.

Go to: **AWS S3 → Bucket → Permissions → CORS**

Add this CORS configuration:

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
        "AllowedOrigins": [
            "https://admin.caffncode.com",
            "https://ut04js0exa.execute-api.eu-north-1.amazonaws.com",
            "http://localhost:3000",
            "http://localhost:*"
        ],
        "ExposeHeaders": ["ETag", "x-amz-version-id"],
        "MaxAgeSeconds": 3000
    }
]
```

## S3 Bucket Public Access

The S3 bucket must also have a policy allowing public read access:

**AWS S3 → Bucket → Permissions → Bucket Policy**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::caffncode-assets/*"
        }
    ]
}
```

## CloudFront / CDN Configuration

If you're serving the bucket through CloudFront:

1. **CloudFront Distribution** should point to the S3 bucket
2. **Allowed HTTP Methods** should include: GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE
3. **Cache policy** should cache based on query strings (for cache-busting `?t=timestamp`)
4. **Origin S3 bucket policy** should allow the Lambda function to write to the bucket

## Testing the Upload

1. Go to Admin Panel → Profile → Upload a new photo
2. Check browser DevTools (F12) → Network tab for the S3 PUT request
3. Look for the 200 OK response from the presigned URL
4. If it fails, check the returned error message in the browser Console

## Troubleshooting

### CORS preflight error: "No 'Access-Control-Allow-Origin' header"
- Verify `https://admin.caffncode.com` is in the S3 bucket CORS **AllowedOrigins**
- Clear browser cache (Shift+Cmd+R on macOS)
- Check AWS S3 console confirms CORS policy was saved

### S3 PUT request fails (403 Forbidden)
- CORS not properly configured on S3 bucket
- Admin domain not in CORS allowed origins
- S3 Bucket Policy may not allow public writes (presigned URLs should work regardless)

### "Failed to generate photo upload URL" error
- Check Lambda environment variables are set correctly
- Check Lambda has IAM permissions for S3 and DynamoDB
- Check Lambda execution role has S3 write permissions

### Photo URL returns 404 or doesn't load
- Verify bucket policy allows public read access (see above)
- Check CloudFront is configured to cache the path
- Verify the CDN URL in the Lambda environment variable matches your setup
- Ensure object exists in S3: AWS S3 Console → caffncode-assets → admins/ folder
