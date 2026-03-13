# AWS File Upload Gallery with SNS Alerts

A file upload and gallery website hosted on AWS with real-time email notifications.

## What This Project Does
- User visits the website
- Uploads a file from browser
- File gets saved to AWS S3 bucket
- Email alert sent automatically via SNS

## Architecture
```
Browser → EC2 (Apache + Flask) → S3 Bucket
                                      ↓
                                 SNS Topic
                                      ↓
                                 📧 Email Alert
```

## AWS Services Used
- **EC2** - Hosts the website (Ubuntu + Apache)
- **S3** - Stores uploaded files
- **IAM Role** - Gives EC2 secure access to S3
- **VPC** - Private network for all resources
- **Route Table** - Directs internet traffic
- **Internet Gateway** - Connects VPC to internet
- **Security Groups** - Firewall for EC2
- **SNS** - Sends email alerts on file upload
- **CloudWatch** - Monitors S3 bucket metrics

## Tech Stack
- Python (Flask) - Backend
- boto3 - AWS Python SDK
- Apache - Web Server + Reverse Proxy
- HTML/CSS/JavaScript - Frontend

## Features
- File upload from browser
- Gallery view of all uploaded files
- Presigned URLs for secure file sharing
- Real time email notification on every upload
- Files stored permanently in S3

## What I Learned
- How EC2, S3, IAM work together
- VPC Networking - Subnets, Route Tables, IGW
- Apache Reverse Proxy to Flask
- boto3 AWS Python SDK
- SNS Topic, Subscriptions and Policies
- S3 Event Notifications triggering SNS
- Presigned URLs for secure file access
- Git and GitHub for version control

## Note
- Python Flask backend developed with Claude AI assistance
- AWS Infrastructure configured manually
