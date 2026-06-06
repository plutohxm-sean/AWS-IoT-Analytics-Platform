# AWS IoT Analytics Platform

## Overview

AWS IoT Analytics Platform adalah implementasi end-to-end IoT data processing pipeline yang dibangun menggunakan layanan AWS. Project ini mengintegrasikan AWS IoT Core, Kinesis, Lambda, Amazon RDS, Amazon S3, SageMaker, API Gateway, CloudFormation, Terraform, dan GitHub Actions untuk membangun solusi IoT yang scalable, automated, dan cloud-native.

Platform ini dirancang untuk mensimulasikan arsitektur IoT modern yang mampu menerima data sensor secara real-time, memproses data menggunakan layanan serverless, menyimpan data ke database dan data lake, serta menyediakan API untuk kebutuhan monitoring dan analisis.

---

## Objectives

Project ini bertujuan untuk:

- Mengimplementasikan komunikasi MQTT menggunakan AWS IoT Core
- Membangun data pipeline berbasis event-driven architecture
- Menggunakan Kinesis untuk real-time data streaming
- Mengotomatisasi proses data menggunakan AWS Lambda
- Menyimpan data ke Amazon RDS MariaDB
- Membangun data lake menggunakan Amazon S3
- Mengintegrasikan machine learning menggunakan Amazon SageMaker
- Menyediakan REST API menggunakan API Gateway
- Mengelola infrastruktur menggunakan CloudFormation dan Terraform
- Mengotomatisasi deployment menggunakan GitHub Actions

---

## Architecture

```text
IoT Device
    │
    ▼
AWS IoT Core
    │
    ├──────────────► IoT Rule #1
    │                    │
    │                    ▼
    │              Kinesis Data Stream
    │                    │
    │                    ▼
    │               Lambda Function
    │                    │
    │                    ▼
    │                Amazon RDS
    │
    └──────────────► IoT Rule #2
                         │
                         ▼
                  Kinesis Firehose
                         │
                         ▼
                      Amazon S3
                         │
                         ▼
                  Amazon SageMaker

API Gateway
     │
     ▼
 Lambda APIs
     │
     ▼
 Amazon RDS
```

---

## Technology Stack

### Cloud Services

- AWS IoT Core
- Amazon Kinesis Data Streams
- Amazon Kinesis Firehose
- AWS Lambda
- Amazon RDS MariaDB
- Amazon S3
- Amazon SageMaker
- Amazon API Gateway
- Amazon VPC
- IAM

### Infrastructure as Code

- AWS CloudFormation
- Terraform

### DevOps

- GitHub Actions
- AWS CLI

### Programming Languages

- Python
- JavaScript / Node.js

---

## AWS IoT Core

AWS IoT Core berfungsi sebagai MQTT Broker yang menerima data sensor dari perangkat IoT.

### Components

#### IoT Thing

Representasi logical dari device fisik.

```text
Thing Name:
lksiot
```

#### IoT Certificate

Digunakan untuk autentikasi device menggunakan TLS.

File yang diperlukan:

```text
certificate.pem.crt
private.pem.key
AmazonRootCA1.pem
```

#### IoT Policy

Policy yang mengatur hak akses device.

Contoh permission:

```json
{
  "Action": [
    "iot:Connect",
    "iot:Publish",
    "iot:Receive",
    "iot:Subscribe"
  ]
}
```

---

## IoT Rules Engine

Rules Engine digunakan untuk meneruskan data dari MQTT Topic ke layanan AWS lainnya.

Topic yang digunakan:

```sql
SELECT * FROM 'datasensor'
```

### Rule 1 - Data Stream Processing

Rule Name:

```text
lks_datastream_<province>
```

Destination:

```text
Amazon Kinesis Data Stream
```

Flow:

```text
IoT Core
    ▼
Kinesis Stream
    ▼
Lambda
    ▼
MariaDB
```

---

### Rule 2 - Data Lake Processing

Rule Name:

```text
lks_firehose_<province>
```

Destination:

```text
Amazon Kinesis Firehose
```

Flow:

```text
IoT Core
    ▼
Firehose
    ▼
Lambda Transform
    ▼
Amazon S3
```

---

## Network Infrastructure

### VPC

```text
CIDR Block:
172.15.0.0/16
```

### Public Subnets

```text
PublicSubnetA
PublicSubnetB
PublicSubnetC
```

### Private Subnets

```text
PrivateSubnetA
PrivateSubnetB
PrivateSubnetC
```

### Additional Components

- Internet Gateway
- NAT Gateway
- Route Tables
- Security Groups

Database Security Group:

```text
Port 3306
MariaDB Access
```

---

## Data Streaming Layer

### Amazon Kinesis Data Streams

Digunakan untuk pemrosesan data real-time.

Example Stream:

```text
lksdatastreamiot
```

### Amazon Kinesis Firehose

Digunakan untuk pengiriman data otomatis ke Amazon S3.

Example Delivery Stream:

```text
LKS-Firehose-2024
```

---

## Database Layer

### Amazon RDS MariaDB

Digunakan sebagai database utama untuk menyimpan data sensor.

Configuration:

```text
Engine: MariaDB
Access: Private
Tier: Free Tier Eligible
```

Contoh Data:

| Device | Temperature | Humidity |
|----------|----------|----------|
| Sensor-01 | 29.5 | 78 |
| Sensor-02 | 31.1 | 74 |

---

## Serverless Processing

### Store Lambda

Menyimpan data sensor dari Kinesis Stream ke Amazon RDS.

```text
Kinesis Stream
      ▼
Store Lambda
      ▼
MariaDB
```

### Transform Lambda

Memproses data dari Firehose sebelum disimpan ke Amazon S3.

```text
Firehose
      ▼
Transform Lambda
      ▼
Amazon S3
```

### API Lambda

Menyediakan endpoint API untuk mengakses data.

```text
API Gateway
      ▼
API Lambda
      ▼
Amazon RDS
```

---

## Storage Layer

### Amazon S3

Bucket yang digunakan:

```text
lks-raw-data
lks-processed-data
lks-backup-data
```

Lifecycle Policy:

```text
30 Days  → Glacier
365 Days → Expire
```

---

## Machine Learning Layer

### Amazon SageMaker

Digunakan untuk analisis dan pemodelan data historis.

Use Cases:

- Forecasting
- Anomaly Detection
- Data Analytics
- Predictive Maintenance

Notebook Instance:

```text
notebook-al2-v3
```

---

## API Layer

### Amazon API Gateway

REST API digunakan untuk mengakses data dari database.

Example Endpoints:

```http
GET /sensor/latest
GET /sensor/history
GET /analytics
```

---

## Infrastructure as Code

### CloudFormation Stacks

```text
lksiot
lkscf-networking
lksdb
lksbucket
lksml
```

CloudFormation digunakan untuk deployment resource AWS secara native.

---

### Terraform Resources

Example Resources:

```hcl
aws_iot_thing
aws_lambda_function
aws_kinesis_stream
aws_db_instance
aws_s3_bucket
```

Terraform digunakan untuk deployment infrastructure secara modular dan repeatable.

---

## CI/CD Pipeline

Deployment dilakukan menggunakan GitHub Actions.

### CloudFormation Deployment

```text
Deploy Networking
        ▼
Deploy Storage
        ▼
Deploy IoT
```

### Terraform Deployment

```text
terraform fmt
      ▼
terraform validate
      ▼
terraform plan
      ▼
terraform apply
```

### Lambda Deployment

```text
store-lambda
transform-lambda
api-lambda
```

---

## MQTT Publisher

Topic:

```text
datasensor
```

Example Payload:

```json
{
  "device_id": "sensor-01",
  "temperature": 29.7,
  "humidity": 78,
  "timestamp": "2026-06-06T10:00:00Z"
}
```

---

## Deployment Guide

### 1. Deploy Networking Stack

Deploy VPC, Subnets, NAT Gateway, dan Security Groups.

Output yang perlu dicatat:

```text
VPC ID
Subnet IDs
Security Group IDs
```

---

### 2. Deploy S3 Bucket Stack

Deploy seluruh bucket storage.

Output:

```text
Raw Bucket
Processed Bucket
Backup Bucket
```

---

### 3. Deploy Kinesis Resources

Deploy:

```text
Kinesis Data Stream
Kinesis Firehose
```

Pastikan status:

```text
ACTIVE
```

---

### 4. Deploy AWS IoT Core

Deploy:

```text
Thing
Certificate
Policy
IoT Rules
```

Simpan informasi berikut:

```text
Certificate ARN
IoT Endpoint
```

---

### 5. Test MQTT Publisher

Publish sample payload ke topic:

```text
datasensor
```

Verifikasi:

- CloudWatch Logs
- Kinesis Metrics

---

### 6. Deploy Amazon RDS

Deploy database MariaDB.

Catat:

```text
RDS Endpoint
```

---

### 7. Deploy Lambda Functions

Deploy:

```text
Store Lambda
Transform Lambda
API Lambda
```

Konfigurasikan:

```text
VPC
Subnets
Security Groups
```

---

### 8. Deploy API Gateway

Deploy REST API.

Verifikasi endpoint menggunakan:

```bash
curl https://api-id.execute-api.region.amazonaws.com/prod
```

---

### 9. Deploy SageMaker

Deploy notebook instance dan hubungkan dengan data lake S3.

---

### 10. Configure CI/CD

Tambahkan GitHub Secrets:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
TOKEN
ALB_ENDPOINT
```

---

### 11. Validate End-to-End Flow

```text
MQTT Publisher
      ▼
AWS IoT Core
      ▼
IoT Rules
      ▼
Kinesis
      ▼
Lambda
      ▼
RDS + S3
      ▼
API Gateway
      ▼
Web Application
```

---

## Troubleshooting

### IoT Rules Deployment Failed

Cause:

```text
Kinesis resource not deployed
```

Solution:

```text
Deploy Kinesis before IoT Rules
```

---

### Lambda Cannot Access Database

Cause:

```text
Incorrect VPC configuration
```

Solution:

```text
Verify Subnet and Security Group IDs
```

---

### Dashboard Shows No Data

Cause:

```text
MQTT Publisher stopped
```

Solution:

```text
Keep publisher running
```

---

## Important Outputs

Simpan seluruh informasi berikut selama deployment:

```text
AWS Account ID
IoT Endpoint
Certificate ARN
RDS Endpoint
Subnet IDs
Security Group IDs
API Gateway URL
Kinesis Stream Name
S3 Bucket Names
```

---

## Learning Outcomes

Melalui project ini, pengguna akan memahami:

- AWS IoT Core
- MQTT Communication
- Kinesis Streaming
- Serverless Architecture
- Amazon RDS Integration
- Data Lake Implementation
- Machine Learning on AWS
- Infrastructure as Code
- CI/CD Automation
- End-to-End Cloud Native Architecture
