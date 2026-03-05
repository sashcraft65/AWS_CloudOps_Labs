# Lab 01 – CloudWatch Alarm with SNS Notification

## Objective
Create a CloudWatch alarm that monitors EC2 CPU utilization 
and sends an SNS email notification when the threshold is breached.

## Services Used
- Amazon EC2 (t2.micro, free tier)
- Amazon CloudWatch
- Amazon SNS
- AWS CloudShell

## Architecture
EC2 Instance → CPUUtilization Metric → CloudWatch Alarm → SNS Topic → Email

## What I Built
- Launched a t2.micro EC2 instance with detailed monitoring enabled
- Created an SNS topic with email subscription
- Created a CloudWatch alarm: CPUUtilization > 40% over 1-minute period
- Used CloudShell CLI to push metric data simulating a CPU spike
- Verified alarm transitioned OK → ALARM and email notification delivered

## Key Concepts Learned
- Default vs detailed monitoring (5-min vs 1-min granularity)
- CloudWatch alarm states: OK, ALARM, INSUFFICIENT_DATA
- SNS topic + subscription model
- Burstable instance CPU credits (t2/t3 family)
- EC2 status checks: Instance vs System level

## Troubleshooting Encountered
- EC2 Instance Connect failed due to default security group 
  blocking port 22 — resolved by using CloudShell instead
- Learned that SSM Session Manager is preferred over SSH 
  in production (no open ports required)

## Exam Relevance
- Domain 1: Monitoring, Logging, Analysis, Remediation (22%)
- Memory usage is NOT a default CloudWatch metric — requires CloudWatch Agent
- StatusCheckFailed_System alarm → EC2 Recover action is a key exam pattern