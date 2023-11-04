# 🚀 Serverless - Other services

## 📢 Simple Notification Service 

Public AWS Service. HA and Scalable (Region resilient).

- Coordinates the sending and delivery of messages
- Messages are `<=256KB` payloads
- **SNS Topics** are the base entity of SNS: **permissions** and **configuration**
- A `Publisher` sends messages to a **TOPIC**
- **TOPICS** have `Subscribers` which receive messages
- SNS is used across AWS for notifications - e.g. CloudWatch & CloudFormation
- Delivery **Status** and Delivery **Retries**
- Cross-Account via TOPIC Policy

> [!NOTE]
> Notifications: HTTP(s), Email, SQS, Mobile Push, SMS Messages & Lambda >>> Filters can be applied 🙂

## 🤖 State Machines

Serverless workflow: `START` -> `**STATES**` -> `END` (states are THINGS which occur).

- `Standard` Workflow (default) - Maximum duration of `1 year`
- `Express` Workflow (high volume, event-processing workloads: IOT, streaming data processing and transformation) - Run for up to `5 minutes`
- Started via API Gateway, IOT Rules, EventBridge, Lambda, etc.
- Created and configured with Amazon States Language - `JSON` Template
- IAM Role is used for permissions

### States

- SUCCEED & FAIL
- WAIT
- CHOICE
- PARALLEL
- MAP: loop on each item
- TASK: single unit of work (Lambda, SNS, SQS, DDB, etc)

## 🦶 Step Functions

Step Functions is based on state machines and tasks. In Step Functions, a workflow is called a state machine, which is a series of **event-driven steps**. Each step in a workflow is called a state. A **Task** state represents a unit of work that another AWS service, such as AWS Lambda, performs. A Task state can call any AWS service or API.
