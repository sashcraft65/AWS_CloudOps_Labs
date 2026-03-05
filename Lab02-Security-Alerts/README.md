# Lab 02 – Suspicious Activity Alerts with CloudTrail + CloudWatch

## Objective
Detect and alert on suspicious AWS account activity — specifically 
root account logins and IAM policy changes — using CloudTrail, 
CloudWatch Metric Filters, and SNS notifications.

## Services Used
- AWS CloudTrail
- Amazon CloudWatch Logs
- CloudWatch Metric Filters
- CloudWatch Alarms
- Amazon SNS
- Amazon GuardDuty
- AWS CloudShell

## Architecture
```
CloudTrail → CloudWatch Logs Log Group → Metric Filter → 
CloudWatch Alarm → SNS Topic → Email Notification
```

## What I Built

### Part A – CloudTrail Configuration
- Created a trail delivering logs to S3 and CloudWatch Logs
- Configured IAM role for CloudTrail → CloudWatch Logs delivery
- Log group: cloudtrail-logs

### Part B – Metric Filters
| Filter | Pattern | Metric |
|--------|---------|--------|
| RootLoginDetected | userIdentity.type = Root AND AwsConsoleSignIn | RootLoginCount |
| IAMPolicyChange | eventSource = iam.amazonaws.com AND Create/Delete/Attach policy | IAMChangeCount |

- Namespace: SecurityMetrics
- Metric value: 1, Default value: 0

### Part C – CloudWatch Alarms
| Alarm | Metric | Statistic | Threshold |
|-------|--------|-----------|-----------|
| RootLoginAlarm | RootLoginCount | Sum | >= 1 |
| IAMChangeAlarm | IAMChangeCount | Sum | >= 1 |

- Missing data treatment: Treat as good
- Action: SNS email notification

### Part D – GuardDuty
- Enabled GuardDuty for automated threat detection
- Monitors CloudTrail, VPC Flow Logs, and DNS logs automatically
- No configuration required — findings appear in GuardDuty console

## Key Concepts Learned
- CloudTrail logs to S3 by default — must explicitly route to 
  CloudWatch Logs for metric filtering
- Metric filters watch log streams and increment a counter 
  on pattern matches
- Sum statistic is correct for security events — Average 
  dilutes the signal and can hide real incidents
- Default value: 0 ensures metric exists even with no activity
- Missing data = treat as good for security count metrics — 
  no activity means nothing happened
- Alert fatigue caused by wrong missing data treatment or 
  wrong statistic choice

## Troubleshooting Encountered
- Alarm not firing — alarm actions were muted, enabled via 
  Actions → Alarm actions → Enable
- Metric not crossing threshold — alarm was using Average 
  statistic, changed to Sum so a single event triggers the alarm

## Exam Relevance
- Domain 4: Security and Compliance (16%)
- CloudTrail + CloudWatch Logs integration is a core exam pattern
- Choosing correct statistic (Sum vs Average) for alarm type
- GuardDuty vs manual metric filters — GuardDuty is automated, 
  metric filters are custom/specific