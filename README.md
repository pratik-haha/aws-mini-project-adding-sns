# AWS File Upload Gallery with SNS Alerts

A file upload and gallery website hosted on AWS with real-time email notifications when a file is uploaded.

---

## Architecture
```
User Browser
     ↓
Internet Gateway
     ↓
Route Table
     ↓
VPC → Public Subnet
     ↓
Security Group (Port 80, 22, 443)
     ↓
EC2 (Ubuntu + Apache + Flask)
     ↓
S3 Bucket (stores files)
     ↓
S3 Event Notification
     ↓
SNS Topic (s3-alert)
     ↓
📧 Email Alert to Me
```

---

## AWS Services Used
- **EC2** - Virtual server to host website
- **S3** - Cloud storage for uploaded files
- **IAM Role** - Secure access from EC2 to S3
- **VPC** - Private network
- **Subnet** - Public section inside VPC
- **Internet Gateway** - Door to internet
- **Route Table** - Traffic directions
- **Security Group** - Firewall rules
- **SNS** - Email notification service
- **CloudWatch** - Monitoring and alarms

---

## Tech Stack
- Python + Flask - Backend
- boto3 - AWS Python SDK
- Apache - Web Server + Reverse Proxy
- HTML + CSS + JavaScript - Frontend

---

## Step by Step - What I Did

### Phase 1 - EC2 Setup
1. Launched EC2 instance (Ubuntu, t3.micro)
2. Created VPC with public subnet
3. Attached Internet Gateway to VPC
4. Configured Route Table:
   - 10.0.0.0/16 → local
   - 0.0.0.0/0 → Internet Gateway
5. Created Security Group with:
   - Port 22 (SSH)
   - Port 80 (HTTP)
   - Port 443 (HTTPS)

### Phase 2 - S3 Setup
1. Created S3 bucket in eu-north-1
2. Unchecked Block All Public Access
3. Created IAM Role with AmazonS3FullAccess
4. Attached IAM Role to EC2 instance

### Phase 3 - Application Setup
1. Installed Apache on EC2:
```
   sudo apt install apache2
```
2. Installed Python and Flask:
```
   pip3 install flask boto3
```
3. Created Flask app (app.py) with:
   - / route → serves HTML gallery page
   - /upload route → receives file, uploads to S3
   - /files route → lists S3 files with presigned URLs
4. Configured Apache Reverse Proxy:
   - ProxyPass / → http://127.0.0.1:5000/
   - ProxyPassReverse / → http://127.0.0.1:5000/

### Phase 4 - SNS Setup (Email Alerts)
1. Created SNS Topic:
   - Went to SNS → Topics → Create Topic
   - Type: Standard
   - Name: s3-alert

2. Created Email Subscription:
   - Clicked Create Subscription
   - Protocol: Email
   - Endpoint: my email address
   - Confirmed subscription via email link

3. Added SNS Access Policy:
   - Allowed S3 service to publish to SNS topic
   - Added this policy to SNS topic:
```json
   {
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "s3.amazonaws.com"
         },
         "Action": "SNS:Publish",
         "Resource": "arn:aws:sns:eu-north-1:ACCOUNT_ID:s3-alert"
       }
     ]
   }
```

4. Configured S3 Event Notification:
   - Went to S3 → Bucket → Properties
   - Event Notifications → Create Event Notification
   - Name: file-upload-alert
   - Event Type: PUT (triggers on every upload)
   - Destination: SNS Topic → s3-alert

### Phase 5 - CloudWatch Setup
1. Created CloudWatch Alarm:
   - Went to CloudWatch → Alarms → Create Alarm
   - Selected Metric: S3 → BucketSizeBytes
   - Selected my bucket
   - Threshold: greater than 0
   - Alarm name: s3-upload-alarm
   - Notification: SNS topic → s3-alert

---

## How SNS Works in This Project
```
1. User uploads file on website
2. Flask receives file
3. boto3 uploads file to S3
4. S3 detects new file (PUT event)
5. S3 publishes message to SNS topic
6. SNS checks all subscribers
7. SNS sends email to me
8. I receive alert in inbox!
```

---

## How CloudWatch Works in This Project
```
1. CloudWatch monitors S3 bucket size
2. Checks BucketSizeBytes metric
3. If size > 0 → ALARM state
4. Alarm triggers SNS topic
5. SNS sends email alert
```

---

## SNS Key Concepts Learned
- **Topic** - Like a WhatsApp group for notifications
- **Publisher** - S3 sends message to topic
- **Subscriber** - My email receives from topic
- **Subscription** - Must confirm via email
- **Access Policy** - Gives S3 permission to use SNS
- **Protocol** - Email, SMS, Lambda, SQS, HTTP

---

## CloudWatch Key Concepts Learned
- **Metrics** - Numbers measured over time (CPU, storage etc)
- **Alarms** - Alerts when metric crosses threshold
- **Logs** - Text records of what happened
- **BucketSizeBytes** - S3 metric for total bucket size
- **Alarm States** - OK, ALARM, INSUFFICIENT_DATA

---

## Problems I Faced and Fixed
| Problem | Cause | Fix |
|---|---|---|
| pip install failed | No internet on EC2 | Added 0.0.0.0/0 route to IGW in Route Table |
| Added route to wrong table | Private route table selected | Found correct public route table |
| SNS topic not showing in S3 | S3 had no permission to use SNS | Added S3 service to SNS Access Policy |
| Files not uploading | Bucket name was YOUR_BUCKET_NAME | Updated actual bucket name in app.py |
| Git push rejected | GitHub had different version | Used git pull then git push |

---

## Note
- Python Flask backend developed with Claude AI assistance
- All AWS infrastructure configured manually
- Learned hands-on by facing and fixing real errors
