<p align="center">
  <img src="https://miro.medium.com/v2/resize:fit:1358/format:webp/0*3HztNM5KW7v9V_v8" width="150">
</p>

<h1 align="center"> AWS Cloud Security Detection Engineering Platform
</h1>

<p align="center">

</p>

## Project Overview

The detection platform uses AWS native services to collect, analyze, and alert on security-relevant events. CloudTrail captures all API activity and stores logs in Amazon S3 for long-term retention while simultaneously streaming events to CloudWatch Logs for near real-time analysis. CloudWatch metric filters and GuardDuty findings are used to detect suspicious behavior and trigger alerts via Amazon SNS.

<img width="1280" height="853" alt="image" src="https://github.com/user-attachments/assets/f40926e3-0e1a-4dee-b1cf-9f6afa022ba7" />


### 📘 Table of Contents
[ Architecture Overview ](#%EF%B8%8F-architecture-overview)

[ Log Storage (S3) ](#%EF%B8%8F--implementation---secure-s3-log-storage-foundation-step)  

[CloudTrail Configuration ](#%EF%B8%8F-implementation---cloudtrail-all-regions) 

 [ Detection Rule 1: Root Account Usage ](#-implementation---detection-rule-1-root-account-usage)
 
 [ Detection Rule 2: Unauthorized API Calls ](#-implementation---detection-rule-2-unauthorized-api-calls-accessdenied)    
 
 [ Detection Rule 3: IAM Policy Changes (Privilege Escalation) ](#-implementation---detection-rule-3-iam-policy-changes-privilege-escalation)   
 
 [ Detection Rule 4: Security Group Changes ](#-implementation---detection-rule-4-security-group-changes)  
 
 [ Detection Rule 5: GuardDuty Integration](#--implementation---detection-rule-5-guardduty-integration)  


 




  


## 🏗️ Architecture Overview

📌 Purpose of the Architecture

The architecture is designed to:
 - Collect security telemetry from AWS
 - Analyze activity for suspicious behavior
 - Alert when risky actions occur
 -  Support investigation through centralized logs.

-----

#### Before building detection lets secure the account.

- Signed in into the Root Account and created an IAM user
 - Assigned AdministratorAccess to the created user (temporary for setup)
 -  Enable MFA
 -  Signed in as IAM user with the created account.

#### 📌 Why this matters

Detection is useless if the account itself is insecure.

<img width="1350" height="637" alt="image" src="https://github.com/user-attachments/assets/f9e9f6ab-7db3-4202-8d30-ddb4faa2bad2" />


## 🗄️  Implementation - Secure S3 Log Storage (Foundation Step)

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




## 🏗️ Implementation - CloudTrail (All Regions)

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




## 🔄 Implementation - CloudWatch Logs Integration

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

## 🔍 Implementation - Detection Rule 1: Root Account Usage

🎯 Objective

Detect any use of the AWS root account and generate an alert.


### 📌 Why This Matters

The root account has full administrative privileges.
Best practice is that it should never be used for daily operations.
Any root activity may indicate:
- Account compromise
- Misconfiguration
- Emergency access

### Root Account Usage Detection

A CloudWatch metric filter was created on CloudTrail logs to detect any usage of the AWS root account. When a root activity event is logged, a CloudWatch alarm is triggered and sends a notification via Amazon SNS. This detection is categorized as high severity due to the unrestricted privileges associated with the root account.

_🔹step (1)_
Created a Metric Filter
<br/>

<img width="1352" height="621" alt="image" src="https://github.com/user-attachments/assets/e4eecbb2-0e52-4886-9401-159a57dd00f4" /> <br/>

<img width="1348" height="633" alt="image" src="https://github.com/user-attachments/assets/c3565431-f382-4caf-901c-9c974c16c0d6" /> <br/>


_🔹step (2)_

### SNS Setup for RootAccountUsageAlarm
- Created SNS topic: security-alerts
-  Added subscription (Email) and confirm subscription

 
 
 ### 📌 Purpose: Ensures CloudWatch alarm notifications are delivered whenever it gets triggered

  _notificatrions will be sent to the eamil i added when the alert gets triggered_

- <img width="1355" height="643" alt="image" src="https://github.com/user-attachments/assets/a962e9ab-beae-4107-bb34-2441750fd4b9" />

_confirmation email for the subscription_

<img width="1169" height="710" alt="image" src="https://github.com/user-attachments/assets/696ad3f9-5cb2-4291-b9d8-7bf3cd4cd2c1" />



  
_🔹step (3)_

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

_🔹step (1)_
I signed in into the root user and performed a read only root activity(signed in and accessed the Billing Dashboard) This will trigger the rules and filter will set earlier. 

### ✅ Alarm Confirmation-1– CloudWatch Confirmed Alert Triggered.

<img width="1343" height="634" alt="image" src="https://github.com/user-attachments/assets/2435974d-3b7e-4d74-80bb-d90c83aab1cf" />

### ✅ Alarm Confirmation-2 – Confirmed email notification was received via SNS topic 'security-alerts'

<img width="768" height="1280" alt="image" src="https://github.com/user-attachments/assets/55d7e1b6-e1ad-4532-8ca5-e9a2678e8c90" />



## 🔍 Implementation - Detection Rule 2: Unauthorized API Calls (AccessDenied)

🎯 Objective:
Detect unauthorized AWS API calls using CloudTrail logs.

_🔹step (1)_   

 - Created metric filter
 -  Filter pattern: { $.errorCode = "AccessDenied" }
 -  Metric namespace: SecurityDetection
 -  Metric name: AccessDeniedCount
 -   Metric value: 1
<img width="1345" height="662" alt="image" src="https://github.com/user-attachments/assets/3645a63d-d56d-43c8-861a-61bd17ed6486" />

<img width="582" height="665" alt="image" src="https://github.com/user-attachments/assets/646c6219-1cd4-44a2-bc7c-25410d798d9c" /> <br/>


 
 _🔹step (2)_
 
 -  Created CloudWatch alarm for the filter  i made.
   - Threshold: AccessDeniedCount >= 3 in 5 minutes  (Access denied 3 times in 5 minues) 
   - Notification: SNS topic security-alerts

### 📌 What this does
it detects and alert on unauthorized API activity using CloudTrail, CloudWatch Logs, and SNS. <br/> 

<img width="1350" height="675" alt="image" src="https://github.com/user-attachments/assets/109959cf-59f9-4d3c-8873-0d6f80908273" />
<img width="1352" height="567" alt="image" src="https://github.com/user-attachments/assets/7218121f-5f92-4cc6-b2e0-5a972dfb4d87" />  <br/>

### 🔍 Safe Test for Detection Rule 2 ( AccessDenied Test)

🎯 Objective
- Generated Acess-Denied Event (i launched an Ec2 instance)
- Met Alarm threshold ( Repeated same action 3 times)
- Confirm Metric increment
- Confirm Alarm State
- Confirm Eamil Notification

_🔹step (1)_   
  Generated Acess-Denied Event by trying to lunch services i am not allowed to use. 
  Note: I created a new IAM user and attached on ReadOnly permission in other to simulate this event trigger.

  <img width="1357" height="615" alt="image" src="https://github.com/user-attachments/assets/ade613aa-346b-4a0c-b1c5-e210a4567c29" />
  <img width="1352" height="628" alt="image" src="https://github.com/user-attachments/assets/0933b5c9-037e-4fb1-bf10-71a3744f254c" />
  <img width="1334" height="510" alt="image" src="https://github.com/user-attachments/assets/f11d9cae-9ba9-4c4d-ba7a-0dbb847983e4" />

 
 
### Confirmd Metric increment ✅
 
<img width="1330" height="536" alt="image" src="https://github.com/user-attachments/assets/31696bbb-3ed0-4eda-a661-925b5d07f89e" /> <br/>

### Confirmed Alarm State ✅

<img width="978" height="620" alt="image" src="https://github.com/user-attachments/assets/d399faf1-cbf8-490b-8e63-a8c7eaec9193" />

### Confirmed Eamil Notification  ✅

 <img width="779" height="1280" alt="image" src="https://github.com/user-attachments/assets/2b690494-3da9-4248-aed3-02d9547404a2" />
 


## 🔍 Implementation - Detection Rule 3: IAM Policy Changes (Privilege Escalation)

🎯 Objective

Detect changes to IAM policies or role attachments that could indicate privilege escalation attempts.

### 📌 Why This Matters

Attackers often escalate privileges by attaching policies or modifying roles, Detecting these changes quickly is critical for SOC visibility.

 _🔹step(1)_  

- Created Metric filter
- Filter pattern: 
 { ($.eventName = "AttachUserPolicy") ||
  ($.eventName = "AttachRolePolicy") ||
  ($.eventName = "PutUserPolicy") ||
  ($.eventName = "PutRolePolicy") }
  
  - Metric namespace: SecurityDetections
 - Metric name: IAMPolicyChangeCount
 
    <img width="1351" height="652" alt="image" src="https://github.com/user-attachments/assets/0cd874cd-4040-42c5-9d0d-68ca990982ce" />

 
     _🔹step (2)_
    
 ### - Created CloudWatch Alarm :
  - Threshold: IAMPolicyChangeCount >= 1 in 5 minutes
   - Notification: SNS topic security-alerts
   -  Verification: Alarm exists, awaiting first IAM policy change event

### 📌 What this does
Triggers when IAM policies or role attachments are modified.  <br/> 

<img width="1314" height="565" alt="image" src="https://github.com/user-attachments/assets/fb006fe8-1b9a-48d1-b185-bff2ea727554" />


### 🔍 Safe Test for Detection Rule 3 (IAM Policy Changes)

🎯 Objective
 -  Verify that metric filter IAMPolicyChangeCount increments
 -  Verify that IAMPolicyChangeAlarm triggers
 -  Verify SNS notification is sent to security-alerts


_i used the user with the ReadOnly plocies attached to carry out this test_

### 📌 What i did:
  _I Created AWS IAM JSON policy that allows a user to see the list of IAM users but prevents attaching or changing any policies_

<img width="1337" height="567" alt="image" src="https://github.com/user-attachments/assets/30bb807e-67b6-41ff-a214-6b73dbbe7e9c" />

 <img width="1252" height="576" alt="image" src="https://github.com/user-attachments/assets/2b4c8ac0-f657-4eb0-aa1a-e652d05fdbef" />

_Now the test user can see the list of user_

<img width="1347" height="632" alt="image" src="https://github.com/user-attachments/assets/7ab40376-0cec-4206-98d0-ca9b90370e35" />

   #### Confirmd Metric increment ✅

   <img width="1343" height="550" alt="image" src="https://github.com/user-attachments/assets/79b3e2cf-4eb6-4ebd-aabc-66bce3d7663d" />


### Confirmed Alarm Triggered ✅
<img width="972" height="609" alt="image" src="https://github.com/user-attachments/assets/fccb123c-22f6-4af0-b2d6-e59183e0b9f5" />



### Confirmed SNS Notification  ✅
<img width="707" height="1280" alt="image" src="https://github.com/user-attachments/assets/e204fab8-ec5f-4c94-98f2-fc32f4feb5e2" />


#### Successfully detected potential privilege escalation attempts ✅

## 🔍 Implementation - Detection Rule 4: Security Group Changes

🎯 Objective:
Detect modifications to security group inbound rules that could expose servers to the public or sensitive ports.

### 📌 Why This Matters
 - Opening ports to 0.0.0.0/0 can expose servers to the internet
 - Sensitive ports include: 22 (SSH), 3389 (RDP)
 - Attackers often modify security groups to gain access

 _🔹step (1)_ 
 
- Created Metric filter
- Filter pattern:
   { ($.eventName = "AuthorizeSecurityGroupIngress") ||
     ($.eventName = "RevokeSecurityGroupIngress") }
  - Metric namespace: SecurityDetections
 - Metric name: SecurityGroupChangeCount

   <img width="580" height="598" alt="image" src="https://github.com/user-attachments/assets/68803a4d-1c00-4c38-aa59-71af04f606db" />


 _🔹step (2)_ 

 -  Created CloudWatch alarm :
 - Threshold: SecurityGroupChangeCount >= 1 in 5 minutes
- Notification: SNS topic security-alerts
-  Verification: Alarm exists, awaiting first security group change event

  <img width="1302" height="545" alt="image" src="https://github.com/user-attachments/assets/111eb720-8bd0-44b7-9f22-0900020eff04" />



### 🔍 Safe Test for Detection Rule 4 (Security Group Changes)

🎯 Objective
-  Verify SecurityGroupChangeCount metric increased
-  Verify that SecurityGroupChangeAlarm triggers
-   Verify SNS notification is sent to security-alerts

_ Again, to carry out this test we will be using the Aceess_Test IAM user_
## 📌 What i did:
  _I Attached the AmazonEC2ReadOnlyAccess to the Access_Test IAM user_ 
  - Attemped to modify an inbound rule in the EC2 security groups so as to simultate the trigger. <br/>
  
<img width="1057" height="525" alt="image" src="https://github.com/user-attachments/assets/1f64d1e2-e041-4c2e-b090-7e4f23f1158a" />


  
   #### Confirmd Metric increment ✅
<img width="1269" height="537" alt="image" src="https://github.com/user-attachments/assets/46af063c-e2c0-4df3-acf6-0f0c4b6d179f" /> <br/>

   

  ### Confirmed Alarm Triggered ✅

 <img width="862" height="545" alt="image" src="https://github.com/user-attachments/assets/2349d010-db32-48ad-a73e-6e3b59e8bf4b" />



### Confirmed SNS Notification  ✅

<img width="945" height="1280" alt="image" src="https://github.com/user-attachments/assets/3ba7e593-a2bb-4960-820d-fe9cf1e74cfa" />

### Successfully detected potential network exposure via security group modifications ✅


## ## 🔍 Implementation - Detection Rule 5: GuardDuty Integration

🎯 Objective:
Integrate Amazon GuardDuty to detect managed threats such as:
 - Credential compromise
 - API abuse
 - Anomalous behavior
-  Crypto mining or brute-force attempts

### 📌 Why This Matters
 - Previous detection rules are custom, log-based detections
 - GuardDuty provides managed, (ML) machine-learning–based detections
- Combining custom and managed detections gives full SOC visibility

 _🔹steps_ 
 - Enabled Guard duty
 - Riview Findings
 - Created CloudWatch EventBridge Rule for automated detection and response
 - Verify Notifications
   
(1)
_GuardDuty will start monitoring CloudTrail, VPC Flow Logs, and DNS logs automatically_

<img width="1332" height="603" alt="image" src="https://github.com/user-attachments/assets/5bbbac39-33c0-4626-983e-760f54ed720c" />


(2)
_These are real-time alerts generated by AWS ML and threat intelligence_

<img width="1315" height="639" alt="image" src="https://github.com/user-attachments/assets/992e1d20-04ca-43a7-86e1-bd6c7e38a0a7" />

(3)
_Created CloudWatch EventBridge Rule for automated detection and response_

<img width="1333" height="596" alt="image" src="https://github.com/user-attachments/assets/f1af9b0d-7aeb-4f3c-8e04-7f3a9fd96554" />
 
 (4)
 Verify Notifications

 I generated Sample findings to show that GuardDuty is fully integrated with  pipeline and i can receive alerts automatically.


<img width="779" height="1280" alt="image" src="https://github.com/user-attachments/assets/cf456cfa-5a47-401e-901f-53873bb893f8" />

<img width="863" height="1280" alt="image" src="https://github.com/user-attachments/assets/e960bb45-b7c7-41ce-a680-836489f75165" />

### Cost Control Note

After validating all detection rules, GuardDuty and EventBridge integrations were disabled to prevent unnecessary charges. This demonstrates cost-awareness and best practices in managing AWS security services in lab environments.

## 📌 Project Conclusion

This project implemented a hands-on AWS Security Detection Engineering lab focused on building, validating, and documenting real SOC detection capabilities using native AWS services.

Five core detection rules were designed and tested:

1. Root Account Usage Detection
2. Unauthorized API Calls (AccessDenied)
3. IAM Policy Changes (Privilege Escalation)
4. Security Group Changes (Network Exposure)
5. GuardDuty Managed Threat Detection

CloudTrail was configured as the primary logging source, with logs streamed into CloudWatch for metric-based detections. CloudWatch metric filters and alarms were created to identify suspicious activity, while SNS was used to deliver security alerts. GuardDuty was integrated to provide managed, machine-learning–based detections and routed through EventBridge for automated alerting.

All detections were safely tested using least-privilege IAM users to simulate real-world attack scenarios without impacting production resources. After validation, cost-incurring services were responsibly disabled, demonstrating strong cost-control practices.


