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



## 🏗️ Architecture Overview

📌 Purpose of the Architecture

The architecture is designed to:
 - Collect security telemetry from AWS
 - Analyze activity for suspicious behavior
 - Alert when risky actions occur
 -  Support investigation through centralized logs.

-----

#### Before building detection, secure the account.

- Signed in into the Root Account and created an IAM user
 - Assigned AdministratorAccess to the created user (temporary for setup)
 -  Enable MFA
 -  Signed in as IAM user with the created account.

#### 📌 Why this matters

Detection is useless if the account itself is insecure.

<img width="1350" height="637" alt="image" src="https://github.com/user-attachments/assets/f9e9f6ab-7db3-4202-8d30-ddb4faa2bad2" />


## 🗄️  Implementation – Secure S3 Log Storage (Foundation Step)

🎯 Objective

 Create a secure, tamper-resistant S3 bucket to store CloudTrail logs for audit, investigation, and forensic purposes.
### Secure Log Storage (Amazon S3)

An Amazon S3 bucket was created to store CloudTrail logs securely. 
- Public access was fully blocked to prevent data exposure.
- Bucket versioning was enabled to protect against log deletion or tampering.
-  Server-side encryption was enabled to protect log data at rest.

_🔹This bucket serves as the central log archive for security investigations and forensic analysis_

  
https://github.com/user-attachments/assets/21a5c026-ba51-45d3-8bdd-a7e637847e3c

<img width="1350" height="686" alt="image" src="https://github.com/user-attachments/assets/70472eec-79b4-4e54-a708-2a4ad607c1d4" />




## 🏗️ Implementation – Step 1: CloudTrail (All Regions)

🎯 Objective

Configured CloudTrail to:
 - Capture AWS API activity across all regions
 - Deliver logs securely into the S3 log archive
 -  Preserve log integrity for investigations and analysis.

   ### CloudTrail Log Collection

CloudTrail was configured as an all-region trail to capture AWS API activity across the account. Logs are delivered to a secure, encrypted S3 bucket with log file integrity validation enabled. Management events were enabled to ensure visibility into high-risk actions such as IAM changes and root account usage. This configuration provides the foundational telemetry for security detection and investigations.


_🔹Cloudtrail Created_

   <img width="1346" height="689" alt="image" src="https://github.com/user-attachments/assets/5e27c62f-d9ce-4f81-a68c-c0d0a03e85c2" />  <br/>

   
   _🔹S3 bucket selected and Log validation enabled_ 


   <img width="1362" height="626" alt="image" src="https://github.com/user-attachments/assets/7c1c1217-2e77-4eeb-9b23-d9ad87814945" />  <br/>
   

   _🔹Management event enabled_ 
      
  

 <img width="1353" height="633" alt="image" src="https://github.com/user-attachments/assets/2e0c9da7-9bf4-407e-b7ad-050fe6b97226" />




## 🔄 Implementation – Step 2:  CloudWatch Logs Integration

🎯 Objective
Stream CloudTrail logs into CloudWatch Logs to enable near real-time security detection and alerting.

### CloudWatch Logs Integration

CloudTrail was integrated with Amazon CloudWatch Logs to enable near real-time analysis of AWS API activity. A dedicated log group was created to centralize security-relevant events, allowing for the creation of metric filters and alarms. This integration enables proactive detection of suspicious behavior rather than relying solely on historical log analysis.

 _🔹Enabled CloudWatch Logs_ 
      

<img width="1349" height="626" alt="image" src="https://github.com/user-attachments/assets/f89fa3ac-9f81-41d0-88d1-dead244cccab" />  <br/>

### ✅ Verification – Confirm Logs Are Flowing

_i carried some actions in the video below to verify if cloudwatch will log my events_

https://screenrec.com/share/ayufk59jTE

 _🔹The events i carried out_ 

<img width="1342" height="618" alt="image" src="https://github.com/user-attachments/assets/ab354300-c878-43ad-8b59-d8bad23654e9" /> <br/>

## 🔍 Implementation – Step 3: Detection Rule 1: Root Account Usage

🎯 Objective

Detect any use of the AWS root account and generate an alert.


### 📌 Why This Matters

The root account has full administrative privileges.
Best practice is that it should never be used for daily operations.
Any root activity may indicate:
- Account compromis
- Misconfiguration
- Emergency access

### Root Account Usage Detection

A CloudWatch metric filter was created on CloudTrail logs to detect any usage of the AWS root account. When a root activity event is logged, a CloudWatch alarm is triggered and sends a notification via Amazon SNS. This detection is categorized as high severity due to the unrestricted privileges associated with the root account.

_🔹step(1)_
Created a Metric Filter
<br/>

<img width="1352" height="621" alt="image" src="https://github.com/user-attachments/assets/e4eecbb2-0e52-4886-9401-159a57dd00f4" /> <br/>

<img width="1348" height="633" alt="image" src="https://github.com/user-attachments/assets/c3565431-f382-4caf-901c-9c974c16c0d6" /> <br/>


_🔹step(2)_

### SNS Setup for RootAccountUsageAlarm
- Create SNS topic: security-alerts
-  Add subscription (Email) and confirm subscription

 
 
 ### 📌 Purpose: Ensures CloudWatch alarm notifications are delivered whenever it gets triggered

  _notificatrions will be sent to the eamil i added when the alert gets triggered_

- <img width="1355" height="643" alt="image" src="https://github.com/user-attachments/assets/a962e9ab-beae-4107-bb34-2441750fd4b9" />

_confirmation email for the subscription_

<img width="1169" height="710" alt="image" src="https://github.com/user-attachments/assets/696ad3f9-5cb2-4291-b9d8-7bf3cd4cd2c1" />



  
_🔹step(3)_

 ### Created an Alarm

  _Linked alarm to SNS topic under In alarm state_
   
 <img width="1346" height="618" alt="image" src="https://github.com/user-attachments/assets/835d1fbe-0c56-499f-b926-511a0427d6b6" />


<img width="1554" height="737" alt="image" src="https://github.com/user-attachments/assets/63a92464-23b7-482d-99b9-6a0d4e3243aa" />


### 🔍 Safe Test for Detection Rule: Root Account Usage

🎯 Objective

Trigger the CloudWatch alarm without performing any dangerous root-level changes, so you can confirm:
 - Metric filter works
 -  Alarm enters ALARM state
 - SNS email notification is sent

_🔹step(1)_
I signed in into the root user and performed a read only root activity(signed in and accessed the Billing Dashboard) This will trigger the rules and filter will set earlier. 

### ✅ Alarm Confirmation-1– CloudWatch Confirmed Alert Triggered.

<img width="1343" height="634" alt="image" src="https://github.com/user-attachments/assets/2435974d-3b7e-4d74-80bb-d90c83aab1cf" />

### ✅ Alarm Confirmation-2 – Confirmed email notification was received via SNS topic 'security-alerts'

<img width="768" height="1280" alt="image" src="https://github.com/user-attachments/assets/55d7e1b6-e1ad-4532-8ca5-e9a2678e8c90" />






<img width="1348" height="585" alt="image" src="https://github.com/user-attachments/assets/e1ee97b3-f3b6-420a-94ef-09e0bbd24c2c" />
