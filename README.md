
# Cross Cutting Initiatives SDO Goals  
**Technologies:** Python, FastAPI, Smithy, TypeScript AWS CDK, AWS  

## Overview  
The **Cross Cutting Initiatives SDO Goals project** was designed to address the complexity of tracking migration statuses for **100,000+ services** spread across multiple fragmented platforms. The goal was to **consolidate disparate systems, simplify workflows, and enable real-time visibility** for both administrators and service owners.  

This platform acts as a **centralized hub** to manage migration data, automate notifications, and drive informed decision-making, significantly reducing manual overhead and improving operational efficiency.  

---

## Problem Context & Root Cause Analysis  
### Key Challenges:  
1. **Fragmented Systems:** Migration data was siloed across multiple internal tools, making it difficult to get an accurate end-to-end view.  
2. **Lack of Real-Time Visibility:** No unified tracking mechanism for due dates or service migration progress.  
3. **Manual Overhead:** Status updates and follow-ups were handled through manual email chains, slowing down execution.  
4. **Scaling Issues:** Existing solutions weren’t designed to scale to **100,000+ services** with varied migration timelines and owners.  

### Root Causes Identified:
- **No Single Source of Truth:** Teams built ad hoc tracking solutions over time.  
- **No Event-Driven Communication:** Lack of automated notification pipelines created delays.  
- **Infrastructure Gaps:** Migration workflows lacked cloud-native automation, making integrations brittle.  

---

## Technical Solution Design  

### 1. **Centralized Data Modeling with Smithy**
- Designed **service schemas in Smithy**, ensuring consistency in defining migration attributes (e.g., service metadata, migration status, due dates, ownership, dependencies).  
- Smithy’s model-first approach allows for **multi-language code generation**, ensuring alignment across frontend, backend, and infrastructure teams.  

---

### 2. **Backend API with Python FastAPI**
- Built a **scalable FastAPI backend** as the central service migration tracker.  
- Features:  
  - **RESTful Endpoints:** CRUD operations for migration records, service owners, and timelines.  
  - **Webhook Endpoints:** Publish events when due dates are nearing or migration updates occur.  
  - **Async I/O with FastAPI:** Supports high concurrency and reduces latency for large data sets.  
  - **JWT-based Authentication:** Secures API access for admins and service owners.  

---

### 3. **Cloud Infrastructure Automation with AWS CDK (TypeScript)**
- **Infrastructure as Code (IaC):**  
  - Deployed FastAPI on AWS Lambda + API Gateway for scalability.  
  - DynamoDB chosen for **low-latency access** to 100K+ service records.  
  - S3 for static asset storage (dashboard UI).  
  - EventBridge + SNS for webhook notifications and Slack/Email delivery.  
- CDK pipelines ensure **automated deployments** and easy rollback strategies.  

---

### 4. **Real-Time Notification System**
- Built an **event-driven pipeline**:  
  - Service updates trigger **EventBridge events**.  
  - Events fan out to **SNS topics** that send notifications via **Slack webhooks and AWS SES (email)**.  
  - Supports dynamic subscription to POCs based on service ownership.  
- Reduces manual reminders, ensuring timely responses.  

---

### 5. **Scalability and Observability**
- Integrated **AWS CloudWatch metrics** and structured logging for troubleshooting.  
- Horizontal scaling supported through Lambda concurrency settings.  
- DynamoDB partition keys optimized for high-volume read/write operations.  

---

## Technical Architecture Diagram (Conceptual)  

```
+----------------+       +----------------------+       +---------------------+
|   Frontend UI  | <---- | FastAPI Backend API | <---- |   DynamoDB Storage  |
+----------------+       +----------------------+       +---------------------+
        |                         |                              |
        v                         v                              |
+----------------+       +----------------------+               |
|  Slack/Email   | <---- |  EventBridge + SNS   | <--------------
+----------------+       +----------------------+
```

---

## Impact  
| Metric                              | Before Solution                 | After Solution                     |
|-----------------------------------|--------------------------------|-----------------------------------|
| **Service Migration Visibility**   | Manual, fragmented spreadsheets | Centralized real-time dashboard   |
| **Notification Workflow**          | Manual follow-ups              | Automated Slack/email alerts      |
| **Admin Overhead**                 | 8+ hrs/week per admin          | < 1 hr/week per admin             |
| **Scale**                          | ~10K services supported        | 100K+ services with no degradation|
| **Error Rate in Reporting**        | ~15%                           | < 2%                              |

---

## Key Learnings and Analysis  
1. **API-First Approach:** Defining Smithy models upfront accelerated cross-team collaboration and ensured consistent schema adoption.  
2. **Serverless + IaC Wins:** AWS Lambda + CDK made the solution scalable while keeping operational costs low.  
3. **Async FastAPI:** Provided excellent performance for a **read-heavy system** with spikes in concurrent requests.  
4. **Event-Driven Patterns:** Improved responsiveness and eliminated manual intervention in notifications.  

---

## Future Enhancements  
- **Analytics Dashboard:** Visualization of migration trends and SLA adherence using AWS QuickSight.  
- **Self-Serve APIs:** Enable service teams to integrate migration status directly into their workflows.  
- **Predictive Alerts:** ML-driven notifications for at-risk migrations based on historical patterns.  
