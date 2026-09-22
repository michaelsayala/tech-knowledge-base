# AWS CloudTrail to Splunk

Simple setup guide for sending AWS CloudTrail logs to Splunk using the **Splunk Add-on for AWS**.

## Architecture

```text
AWS CloudTrail
      ↓
     S3
      ↓
     SQS
      ↓
Splunk Add-on for AWS
      ↓
    Splunk
```

## 1. Create an S3 Bucket

Create an S3 bucket for CloudTrail logs.

Example:

```text
splunk-cloudtrail-logs
```

---

## 2. Create a CloudTrail Trail

Go to:

```text
AWS Console → CloudTrail → Trails → Create trail
```

Configure:

```text
Trail Name: splunk-cloudtrail
S3 Bucket: splunk-cloudtrail-logs
```

Enable the required Management Events.

Verify that CloudTrail is writing logs to S3:

```text
S3 → splunk-cloudtrail-logs → AWSLogs/
```

---

## 3. Create an SQS Queue

Go to:

```text
AWS Console → SQS → Create queue
```

Example:

```text
splunk-cloudtrail-queue
```

---

## 4. Configure S3 → SQS

Go to:

```text
S3 → splunk-cloudtrail-logs
→ Properties
→ Event notifications
```

Create an event notification:

```text
Event type: Object Created
Destination: SQS
Queue: splunk-cloudtrail-queue
```

This sends a message to SQS whenever CloudTrail creates a new log file.

---

## 5. Configure SQS Queue Policy

Allow S3 to send messages to the queue.

Required permission:

```text
sqs:SendMessage
```

Restrict the policy to your CloudTrail S3 bucket.

---

## 6. Create IAM Permissions for Splunk

The AWS account used by Splunk needs access to S3 and SQS.

### S3

```text
s3:ListBucket
s3:GetObject
```

### SQS

```text
sqs:GetQueueAttributes
sqs:ReceiveMessage
sqs:DeleteMessage
sqs:ChangeMessageVisibility
```

### JSON Policy
https://splunk.github.io/splunk-add-on-for-amazon-web-services/CloudTrail/
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sqs:GetQueueAttributes",
                "sqs:ListQueues",
                "sqs:ReceiveMessage",
                "sqs:GetQueueUrl",
                "sqs:DeleteMessage",
                "s3:Get*",
                "s3:List*",
                "s3:Delete*"
            ],
            "Resource": [
               "*"
            ]
        }
    ]
}
```



Use an IAM user or IAM role dedicated to Splunk.

---

## 7. Configure the Splunk Add-on for AWS

In Splunk:

```text
Apps → Splunk Add-on for AWS
```

Configure the AWS account under:

```text
Configuration → Account
```

Enter the AWS credentials/role information.

---

## 8. Create the SQS-Based S3 Input

Go to:

```text
Inputs → Create New Input
```

Select:

```text
SQS-Based S3
```

Configure:

```text
AWS Account: aws-cloudtrail
SQS Queue: splunk-cloudtrail-queue
Region: ca-central-1
Index: aws
```

Enable the input.

---

## 9. Verify Data in Splunk

Search:

```spl
index=aws sourcetype=aws:cloudtrail
```

Check the latest events:

```spl
index=aws sourcetype=aws:cloudtrail
| table _time eventName eventSource sourceIPAddress
| sort - _time
```

---

## 10. Troubleshooting

### CloudTrail → S3

Check:

```text
S3 → AWSLogs/
```

If there are no logs, check the CloudTrail trail.

### S3 → SQS

Check whether SQS is receiving messages.

Verify the S3 event notification and SQS queue policy.

### SQS → Splunk

Check:

* AWS credentials
* S3 permissions
* SQS permissions
* Queue name
* AWS region
* Splunk input status

## Final Flow

```text
CloudTrail
    ↓
S3
    ↓
SQS
    ↓
Splunk Add-on for AWS
    ↓
index=aws
```
