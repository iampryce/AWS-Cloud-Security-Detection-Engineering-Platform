<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1358/format:webp/0*3HztNM5KW7v9V_v8" width="150">
</p>

<h1 align="center"> AWS Cloud Security Detection Engineering Platform
</h1>

<p align="center">

</p>




## Project Overview

The detection platform uses AWS native services to collect, analyze, and alert on security-relevant events. CloudTrail captures all API activity and stores logs in Amazon S3 for long-term retention while simultaneously streaming events to CloudWatch Logs for near real-time analysis. CloudWatch metric filters and GuardDuty findings are used to detect suspicious behavior and trigger alerts via Amazon SNS. AWS Lambda enriches alerts with contextual information to support faster investigations.

### 📘 Table of Contents
 1. Project Overview   
 2. Detection Goals
 3. Architecture Overview
 4. Implementation
 - CloudTrail Configuration
- Log Storage (S3)
- Detection Rules (CloudWatch)
- GuardDuty Integration
- Alerting & Notifications
 5. Detection Use Cases
 6. Testing & Validation
 7. Lessons Learned 



 ## 🔹 Detection Goals

The purpose of this detection platform is to identify and alert on high-risk security events within the AWS environment. The following detection goals were defined before implementing logging and monitoring controls:

1. Root Account Usage

2. IAM Privilege Escalation

3. Unauthorized API Calls

4. Sensitive Data Access (S3)

5. Known Malicious Activity




### 🏗️ Architecture Overview

📌 Purpose of the Architecture

The architecture is designed to:
 - Collect security telemetry from AWS
 - Analyze activity for suspicious behavior
 - Alert when risky actions occur
 -  Support investigation through centralized logs


## 🏗️ Implementation – Step 1: CloudTrail (All Regions)

🎯 Objective

Enable all-region CloudTrail to capture all AWS API activity for security monitoring, threat detection, and investigation.

- Create a secure S3 Bucket













