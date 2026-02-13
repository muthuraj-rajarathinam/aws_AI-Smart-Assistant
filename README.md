# AI-Powered HR Assistant using Amazon Bedrock

## 📌 Project Overview

The HR team is currently overwhelmed.

They handle more than 500 employee queries every single day  most of them related to policies, benefits, leave balances, and internal procedures. On top of that, responses are limited to business hours, and the team can only manage a certain number of conversations at a time.

This project solves that problem by building an intelligent, scalable HR assistant using **Amazon Bedrock**. The assistant can:

* Instantly answer HR-related questions using company handbook data
* Submit vacation requests
* Check available leave balances
* Operate 24/7 without human intervention

The goal is simple: reduce HR workload while improving employee experience.

---

## 🏗️ Architecture Overview

This solution uses the following AWS services:

* **Amazon Bedrock Knowledge Base** – Stores and retrieves HR handbook information
* **Amazon Bedrock Agent** – Understands user queries and decides what action to take
* **Action Groups (AWS Lambda)** – Executes tasks like submitting leave requests
* **Amazon DynamoDB** – Stores leave records and request data
* **Amazon S3** – Stores HR handbook documents
* **Amazon OpenSearch Serverless** – Powers semantic search for the knowledge base

The assistant can either:

1. Answer a question using the knowledge base, or
2. Trigger an action (like submitting a leave request).

---

## 🚀 Features

### ✅ Smart HR Question Answering

The assistant pulls answers directly from official HR handbook documents. Employees can ask questions like:
`
* “What is the maternity leave policy?”
* “How many sick leaves do I get per year?”
* “What is the reimbursement process?"
`
Responses are accurate, contextual, and based only on uploaded HR documents.

---

### ✅ Vacation Request Submission

Employees can submit leave requests conversationally:

> “I want to apply for leave from March 5th to March 8th.”

The agent:

* Extracts dates
* Validates inputs
* Calls a Lambda function
* Stores the request in DynamoDB

---

### ✅ Check Leave Balance

Employees can ask:

> “when is my vacation start”

The agent retrieves data from DynamoDB using a Lambda action and responds instantly.

---

## 🛠️ Implementation Steps

Got it — here’s the **correct order from scratch**, short and clear 👇

---

## ✅ Step-by-Step 

### Step 1️⃣: Create OpenSearch Serverless

**Create Collection**

* Type: **Hr Vector search**
* Name: `hr-knowledge-collection`
* Wait until status = Active

---

**Create Vector Index**

Go to:
OpenSearch → Dashboards → Dev Tools

Create index then Vector Fields


---

### Step 2️⃣: Create S3 Bucket & Upload HR Documents

* Name: hr-bedrock-kb-docs
* Disable public access
* Enable encryption

**Upload:**

* HR handbook PDF
* Leave policies
* Benefits documents

---

### Step 3️⃣: Create Bedrock Knowledge Base

Now OpenSearch will appear.

* Data source: S3(connect to the bucket)
* Embedding model: Titan
* Vector store: OpenSearch ( choose opensearch serverless)
* Collection: `hr-knowledge-collection`
* Index: `hr-kb-index`
* Vector field: `vector_field`
* Text field: `text`
* Metadata field: `metadata`
* IAM role: select created role

Sync data.
Now the assistant understands HR policies.

---

### Step 4️⃣: Create the Amazon Bedrock Agent

1. Define the agent’s role and instructions.
2. Attach the Knowledge Base.
3. Configure foundation model (e.g., Amazon Titan or other supported models).
4. Enable conversation memory (optional but recommended).

The agent now knows how to answer policy-related questions.

---

### Step 5️⃣: Create Lambda Functions

**You need two functions:**

 -  Lambda 1: Submit Leave
 -  Function name: submit_leave

Responsibilities:

    Accept employee_name
    startDate
    endDate

Which Store in DynamoDB

Attach IAM policy:
  `Dynamodb:PutItem`
  
 ---
 
 -  Lambda 2: Check Leave Balance
 -  Function name: get_leave_details

Responsibilities:

   `Query DynamoDB
    Return result`

Attach IAM policy:
  `dynamodb:GetItem
   dynamodb:Query`

---

### Step 6️⃣: Create Action Groups

Action groups allow the agent to perform real operations.

#### Action Group 1: Submit Vacation Request

* Backend: AWS Lambda
* Function: `submit_leave`
* Stores request details in DynamoDB

#### Action Group 2: Check Leave Balance

* Backend: AWS Lambda
* Function: `get_leave_details`
* Retrieves leave date from DynamoDB

Define the API schema so the agent knows:

* Required parameters
* Data types
* Expected outputs

---

### Step 7️⃣: DynamoDB Setup

Create a table with:

* **Partition Key:** `employee_name`

This structure allows efficient storage and retrieval of leave records.

---

### Step 8️⃣: Test the Agent

Test scenarios:

* Ask policy questions
* Submit vacation requests
* Check leave balances
* Try edge cases (invalid dates, missing inputs)

Make sure:

* Knowledge responses come only from uploaded documents
* Action groups trigger correctly
* Errors are handled gracefully

---
## 🔥 Final Flow

User → Agent
→ Q&A → Knowledge Base → OpenSearch
→ Action → Lambda → DynamoDB

## 📈 Benefits of This Solution

* 24/7 HR support
* Handles unlimited simultaneous requests
* Reduces HR workload significantly
* Faster response times
* Scalable architecture
* Secure and compliant (AWS-managed services)

---

## 🔐 Security Considerations

* IAM roles with least privilege
* Encrypted S3 buckets
* Secure Lambda permissions
* Controlled Bedrock access policies

---

## 💡 Future Enhancements

* Slack / Microsoft Teams integration
* Multi-language support
* Approval workflow for managers
* Analytics dashboard for HR insights
* Role-based access control

---

## 🎯 Final Outcome

Instead of HR manually answering repetitive questions all day, employees now interact with an intelligent assistant that:

* Understands natural language
* Pulls answers from official HR documentation
* Executes real HR actions
* Works instantly, anytime

This transforms HR from reactive support to strategic impact.
