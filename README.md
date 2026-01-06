# Building a Serverless API 🚀

Hands-on guided project from Coursera **"Python for Serverless Applications and Automation on AWS"** course, completed using AWS Skill Builder lab environment (SPL-CX-100-CEBSAP-1).  

This project demonstrates building, deploying, and testing a serverless HTTP API using **AWS SAM**, **Lambda**, **API Gateway**, and **S3** with Python + Boto3.

---

## 🚀 Table of Contents

- [Lab Overview](#lab-overview)  
- [Lab Sections](#lab-sections)  
  - [1. Connect to AWS Code Editor](#1-connect-to-aws-code-editor)  
  - [2. Review Application Files](#2-review-application-files)  
  - [3. Build and Deploy with AWS SAM](#3-build-and-deploy-with-aws-sam)  
  - [4. Test Initial API Response](#4-test-initial-api-response)  
  - [5. Update Lambda to Read S3 Object](#5-update-lambda-to-read-s3-object)  
- [Key Learnings & Best Practices](#key-learnings--best-practices)  
- [🌟 Observations & Console Evolution](#-observations--console-evolution)  
- [Evidence of Completion](#evidence-of-completion)   

---

## Lab Overview

**Objective:**  
Deploy a serverless GET API that reads the contents of an S3 object (`object1`) from a bucket starting with `sourcefiles` and returns it as JSON.  

**Architecture:**  
Browser/curl → **API Gateway (HTTP API)** → **Lambda (Python 3.9)** → **S3** (get_object).  

**Environment:**  
- AWS Code Editor workspace (pre-provisioned with IAM role).  
- Files reviewed: `app.py` (Lambda handler), `requirements.txt` (boto3), `template.yaml` (SAM template).  
- Deployment: `sam build` + `sam deploy --guided`.  

Screenshots stored in `screenshots/` folder (Code Editor, SAM deploy, API response, curl test, CloudFormation stack, etc.).

---

## Lab Sections

### 1. Connect to AWS Code Editor

**Goal:**  
Launch the pre-configured IDE and familiarize with the interface.

**Steps:**  
- Pasted the **LabInstanceURL** into a new browser tab.  
- Closed welcome tabs and expanded the terminal pane.  
- Navigated file tree to `~/environment/LabFunction/`.

**Key Learnings:**  
- AWS Code Editor provides temporary IAM credentials for workspace access to AWS services.  
- File navigator (left), editor (center), terminal (bottom).

---

### 2. Review Application Files

**Goal:**  
Understand the three core files before deployment.

**Steps:**  
- **app.py**: Lambda handler imports boto3, creates S3 client, defines `lambda_handler(event, context)`. Currently returns static text `"These are not the contents you are looking for."`.  
- **requirements.txt**: Lists `boto3` dependency for AWS SDK.  
- **template.yaml**: SAM template defines:
  - `LabFunction` (AWS::Serverless::Function: CodeUri, Handler, Runtime=python3.9, Events=HTTP GET).  
  - `HttpApi` (AWS::Serverless::HttpApi).  
  - IAM policies for S3 read access.  
  - Output: `HttpApiUrl`.

**Key Learnings:**  
- SAM `Transform` converts YAML to CloudFormation stack.  
- Identified code gap: need `s3.get_object()` + `obj["Body"].read()` to fetch real S3 contents.

---

### 3. Build and Deploy with AWS SAM

**Goal:**  
Package and deploy the serverless app.

**Steps:**  
cd ~/environment
sam build
sam deploy --guided

- Stack name: `sam-app`.  
- Region: lab region (e.g., us-west-2).  
- Copied output `HttpApiUrl` (e.g., `https://dugufgjghh.execute-api.us-west-2.amazonaws.com/labfunction`).

**Key Learnings:**  
- `sam build` compiles code, installs dependencies (pip for boto3), creates `.zip` package.  
- `sam deploy` uploads to S3, creates/updates CloudFormation stack (API Gateway + Lambda).

---

### 4. Test Initial API Response

**Goal:**  
Verify deployment with static response.

**Steps:**  
- Browser: Pasted API URL → `{"result": "These are not the contents you are looking for."}`.  
- Terminal:

echo; curl https://dugufgjghh.execute-api.us-west-2.amazonaws.com/labfunction; echo; echo

→ Same JSON response.

**Key Learnings:**  
- curl + `echo` for clean output.  
- Confirms end-to-end: API Gateway → Lambda → static return.

---

### 5. Update Lambda to Read S3 Object

**Goal:**  
Modify code to fetch real S3 contents and redeploy.

**Steps:**  
- In `app.py`, updated `contents` variable:
```python
obj = s3.get_object(Bucket='sourcefiles...', Key='object1')
contents = obj["Body"].read()
```
Saved, then:

sam build
sam deploy

Retested browser/curl → {"result": "Welcome to AWS Lambda!"}.

Key Learnings:

Boto3 get_object() returns StreamingBody; use .read() for string contents.

SAM enables fast iterations (build/deploy in seconds).

### Key Learnings & Best Practices

SAM workflow: template.yaml → sam build (package) → sam deploy (CloudFormation stack).

Use HTTP APIs (not REST APIs) for simple, low-cost serverless backends.

Lambda IAM policies must grant explicit S3 read access (Bucket/Key).

Verify S3 object contents with aws s3 cp or console download.

AWS Code Editor is ideal for browser-based serverless dev without local setup.

### 🌟 Observations & Console Evolution

SAM CLI auto-saves deploy params to samconfig.toml (no re-prompts on redeploy).

AWS Code Editor terminal supports full AWS CLI + SAM commands seamlessly.

API Gateway HTTP APIs have simpler auth (no CORS issues in basic GET tests).

CloudFormation stack shows clear resource relationships (Lambda → API → IAM).

### Evidence of Completion

Successfully deployed serverless API via SAM (CloudFormation stack sam-app).

Before: 
```python
{"result": "These are not the contents you are looking for."}
```
After: 
```python
{"result": "Welcome to AWS Lambda!"} (from S3 object1).
```

Coursera course: Python for Serverless Applications and Automation on AWS (completion cert pending).

Screenshots: Code Editor, SAM output, API responses (before/after), curl command, CloudFormation stack.

