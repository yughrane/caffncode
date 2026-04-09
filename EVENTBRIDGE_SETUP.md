# EventBridge Setup for clipdrop-cleanup

## Quick Steps (AWS Console)

### 1. Go to EventBridge
- Open AWS Console
- Search for **EventBridge**
- Click **Rules** (under Events)
- Click **Create rule**

### 2. Configure Rule Details
**Name:** `clipdrop-cleanup-daily`  
**Description:** Auto-delete expired ClipDrop rooms daily  
**Event bus:** Default  
**Rule type:** Schedule

### 3. Set Schedule
Select: **Cron expression**

**Cron Expression:**
```
0 2 * * ? *
```

This means: **2:00 AM UTC every day**

Breakdown:
- `0` = minute (0)
- `2` = hour (2 AM UTC)
- `*` = day of month (any)
- `*` = month (any)
- `?` = day of week (any)
- `*` = year (any)

### 4. Select Target
**Target 1:**
- Select target → **AWS service**
- Service → **Lambda**
- Function → Select **clipdrop-cleanup**
- Execution role → Create new role OR select existing role with Lambda invoke permissions

### 5. Review & Create
- Click **Create rule**

---

## Alternative: Different Schedules

Want a different time? Use these cron expressions:

| Time (UTC) | Cron Expression |
|-----------|-----------------|
| 12:00 AM (midnight) | `0 0 * * ? *` |
| 2:00 AM | `0 2 * * ? *` |
| 6:00 AM | `0 6 * * ? *` |
| 12:00 PM (noon) | `0 12 * * ? *` |
| 3:00 PM | `0 15 * * ? *` |
| Every 6 hours | `0 0/6 * * ? *` |
| Every Monday at 2 AM | `0 2 ? * MON *` |

---

## Verify It Works

### Check Rule is Active
1. Go to **EventBridge → Rules**
2. Find **clipdrop-cleanup-daily**
3. Status should show: ✅ **Enabled**

### View Past Executions
1. Go to **Lambda → clipdrop-cleanup**
2. Click **Monitor** tab
3. See recent invocations in **Invocations** graph

### Check CloudWatch Logs
1. Go to **CloudWatch → Log groups**
2. Find: `/aws/lambda/clipdrop-cleanup`
3. View recent logs to confirm execution

---

## Common Issues

### Rule created but Lambda not triggering
- [ ] Check Lambda execution role has `events:InvokeFunction` permission
- [ ] Verify Lambda function exists and is in the same region (eu-north-1)
- [ ] Check rule is **Enabled** (not disabled)

### "Access Denied" error
- Lambda IAM role needs this policy:
```json
{
  "Effect": "Allow",
  "Action": "lambda:InvokeFunction",
  "Resource": "arn:aws:lambda:eu-north-1:*:function:clipdrop-cleanup"
}
```

### Rule exists but shows wrong schedule
- Edit the rule and verify the **Cron Expression**
- Click **Edit** on the rule in EventBridge console
- Scroll to "Schedule" section and update cron expression

---

## Test Manually (Optional)

Want to test without waiting for the schedule?

### Via AWS CLI:
```bash
aws events put-events \
  --entries '[{
    "Source": "test",
    "DetailType": "test",
    "Detail": "{}",
    "EventBusName": "default"
  }]' \
  --region eu-north-1
```

### Via AWS Console:
1. Lambda console → **clipdrop-cleanup**
2. Click **Test**
3. Event name: "EventBridge"
4. Paste this template:
```json
{
  "version": "0",
  "id": "test-event",
  "detail-type": "Scheduled Event",
  "source": "aws.events",
  "account": "123456789012",
  "time": "2026-04-09T02:00:00Z",
  "region": "eu-north-1",
  "resources": ["arn:aws:events:eu-north-1:123456789012:rule/clipdrop-cleanup-daily"],
  "detail": {}
}
```
5. Click **Test**
6. Check logs to verify it works

---

## Summary

✅ EventBridge Rule Name: `clipdrop-cleanup-daily`  
✅ Trigger: Daily at `2:00 AM UTC` (customize as needed)  
✅ Target: Lambda function `clipdrop-cleanup`  
✅ Region: `eu-north-1`  
✅ Verify in CloudWatch Logs after first execution
