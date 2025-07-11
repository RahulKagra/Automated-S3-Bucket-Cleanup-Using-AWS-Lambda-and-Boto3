
# 🧹 Automated S3 Bucket Cleanup Using AWS Lambda and Boto3

## 🎯 Objective

This project demonstrates the use of **AWS Lambda** and **Boto3 (Python SDK)** to automatically **delete files older than 30 days** from an S3 bucket. This improves storage hygiene, cost efficiency, and automation skills using serverless computing.

---

## 🗂️ S3 Bucket Setup

1. Created an S3 bucket named: `s3-cleanup-assignment-rahulk` .
2. Uploaded multiple files including test files that simulate being older than 30 days by adjusting logic during testing (see code).
3. Bucket was set up with no versioning, public access was blocked, and default settings were retained.

---

## 🔐 IAM Role for Lambda

- IAM Role Name: `LambdaS3CleanupRole`
- Attached Policy: `AmazonS3FullAccess`  
  *(Note: For real-world use, apply least-privilege principle with a custom policy.)*
- Role was assigned to the Lambda function to allow listing and deleting objects in S3.

---

## 🧠 Lambda Function Setup

- Lambda Function Name: `S3CleanupFunction`
- Runtime: `Python 3.12`
- Region: `us-east-1` (or as configured)
- Function Purpose:
  - Connect to a specific S3 bucket
  - List all objects
  - Check the `LastModified` timestamp
  - Delete files older than 30 days
  - Log deleted files

---

## 🧾 Lambda Code Snippet (core logic)

```python
import boto3
from datetime import datetime, timezone, timedelta

BUCKET_NAME = 's3-cleanup-assignment-rahul'

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    deleted_files = []

    current_time = datetime.now(timezone.utc)

    response = s3.list_objects_v2(Bucket=BUCKET_NAME)

    if 'Contents' in response:
        for obj in response['Contents']:
            last_modified = obj['LastModified']
            file_age = current_time - last_modified

            if file_age > timedelta(days=30):
                s3.delete_object(Bucket=BUCKET_NAME, Key=obj['Key'])
                deleted_files.append(obj['Key'])

    if deleted_files:
        print("Deleted files:")
        for key in deleted_files:
            print(key)
    else:
        print("No files older than 30 days found.")

    return {
        'statusCode': 200,
        'body': f'Deleted files: {deleted_files}'
    }
```

---

## 🧪 Testing Procedure

1. **Manual Invocation**:
   - Created a test event with no input
   - Ran Lambda function from AWS Console

2. **Result Validation**:
   - Checked CloudWatch logs for output
   - Verified that files older than 30 days (or simulated to be old) were deleted
   - Confirmed that only recent files remain in the bucket

---

## 📸 Screenshots

| Description                | Screenshot Link |
|---------------------------|------------------|
| S3 Bucket with files       | ![](screenshots/https://github.com/RahulKagra/Automated-S3-Bucket-Cleanup-Using-AWS-Lambda-and-Boto3/blob/main/Test%20of%20Setup.png)|
re.png) |
| IAM Role setup             | ![](screenshots/iam-role.png) |
| Lambda Function code       | ![](screenshots/lambda-code.png) |
| Manual test output         | ![](screenshots/lambda-output.png) |
| S3 Bucket after cleanup    | ![](screenshots/s3-bucket-after.png) |

> Replace the image file names above if your screenshots are named differently.

---

## ✅ Conclusion

This assignment provided hands-on experience with:
- Automating cloud tasks using Lambda
- Using Boto3 to interact with AWS services
- Managing IAM roles and permissions
- Testing event-driven cleanup systems for S3 storage

---

## 📁 Project Structure

```
s3-cleanup-lambda/
├── lambda_function.py
├── README.md
└── screenshots/
    ├── s3-bucket-before.png
    ├── s3-bucket-after.png
    ├── lambda-code.png
    ├── iam-role.png
    └── lambda-output.png
```
