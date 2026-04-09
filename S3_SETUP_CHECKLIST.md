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

The S3 bucket (`caffncode-assets` or similar) must have CORS enabled to allow direct uploads from the browser.

Go to: **AWS S3 → Bucket → Permissions → CORS**

Add this CORS configuration:

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
        "AllowedOrigins": [
            "https://ut04js0exa.execute-api.eu-north-1.amazonaws.com",
            "http://localhost:*"
        ],
        "ExposeHeaders": ["ETag", "x-amz-version-id"],
        "MaxAgeSeconds": 3000
    }
]
```

Replace `ut04js0exa.execute-api.eu-north-1.amazonaws.com` with your actual API Gateway domain.

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

### "Failed to generate photo upload URL" error
- Check Lambda environment variables are set correctly
- Check Lambda has IAM permissions for S3 and DynamoDB

### S3 PUT request fails (403 Forbidden)
- CORS not properly configured on S3 bucket
- API Gateway domain not in CORS allowed origins

### Photo URL returns 404 or doesn't load
- Verify bucket policy allows public read access (if public)
- Check CloudFront is configured to cache the path
- Verify the CDN URL in the Lambda environment variable matches your setup
