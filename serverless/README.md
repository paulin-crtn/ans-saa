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

## 🚌 Simple Queue Service

Public AWS Service. HA, Scalable (Region resilient) and Fully Managed.

- Great for decoupling applications, worker pools, Batch for future processing, etc.
- Messages `<=256KB` in size
- Received messages are **hidden** (`VisibilityTimeout`)... then either reappear (retry) or are explicitly deleted
- **Dead-Letter Queues** can be used for problem messages
- ASGs can scale and Lambdas invoke based on queue length
- A single request can have `1-10` messages, up to a maximum total payload of `256KB`
- Each `64KB` chunk of a payload is billed as 1 request (for example, an API action with a 256 KB payload is billed as 4 requests)
- Poll a SQS Queue: **Short** (immediate but can bill 1 request for 0 message) or **Long** (`waitTimeSeconds`, up to `20s`)
- Access to a queue is based on identity policies or queue policies (only queue policy can allow access from external accounts)
- Encryption at rest (KMS) & in-transit

> [!NOTE]
> SNS and SQS Fanout: Message is added into an SNS Topic and multiple SQS subscribed to it

### FIFO

- Guarantee exactly once delivery: **duplicates are removed**
- Guarantee the order: First-In First-Out (great when we need to process in a sequential order)
- Up to `300` transactions per second, or `3,000` messages with batching (300 transactions of 10 messages) => Not great with scaling
- Must have `.fifo` suffix in order to be a valid FIFO queue

### Standard

- Guarantee **at least once delivery** (but could be more 😬)
- No guarantee on the order of that delivery (Best-Effort Ordering)
- More like multi-lane highways => Great for scaling because it's easy to add a lane (near unlimited Transaction Per Second)

## 🤖 State Machines

Serverless workflow: `START` -> `**STATES**` -> `END` (states are THINGS which occur).

- `Standard` Workflow (default) - Maximum duration of `1 year`
- `Express` Workflow (high volume, event-processing workloads: IOT, streaming data processing and transformation) - Run for up to `5 minutes`
- Started via API Gateway, IOT Rules, EventBridge, Lambda, etc.
- Created and configured with Amazon States Language - `JSON` Template
- IAM Role is used for permissions

#### States

- SUCCEED & FAIL
- WAIT
- CHOICE
- PARALLEL
- MAP: loop on each item
- TASK: single unit of work (Lambda, SNS, SQS, DDB, etc)

## 🦶 Step Functions

Step Functions is based on state machines and tasks. In Step Functions, a workflow is called a state machine, which is a series of **event-driven steps**. Each step in a workflow is called a state. A **Task** state represents a unit of work that another AWS service, such as AWS Lambda, performs. A Task state can call any AWS service or API.

## 🚪 API Gateway

Public AWS Service. **Create** and **manage** APIs. 

- Endpoint/entry-point for applications
- Sits between applications (clients) & integrations (services)
- Highly available, scalable, handles authorisation, throttling, caching, CORS, transformations, OpenAPI spec, direct integration, etc.
- Can connect to services/endpoints in AWS or on-premises
- HTTP APIs, REST APIs and WebSocket APIs
- `29 seconds` for any requests to API Gateway

### 👀 Overview

1. REQUEST: Authorize, Validate, Transform
2. Integrations (DDB, SNS, Lambda, etc.)
3. RESPONSE: Transform, Prepare, Return

> [!NOTE]
> CloudWatch Logs can store and manage full stage request and response logs

> [!NOTE]
> API Gateway Cache can be used to reduce the number of calls made to backend integrations and improve client performance

### 👋 Authentication 

- Authenticate with `Cognito` and receive token: token verification is handled natively
- Client calls API Gateway with a Bearer token: call a custom Lambda Authorizer -> call to ID provider to check token validity

### 📍 Endpoint Types

- **Edge-Optimized**: routed to the nearest CloudFront POP (Point Of Presence)
- **Regional**: clients in the same region (does NOT use CloudFront POP)
- **Private**: endpoint accessible only within a VPC via Interface Endpoint

### 🏟️ Stages

- APIs are deployed to stages (e.g. test, prod, etc.)
- Each of these stages has its own unique endpoint URL
- Stages can be enabled for canary deployments (if done, deployments are made to the canary not the stage)

> [!NOTE]
> Stages enabled for canary deployments can be configured so a certain percentage of traffic is sent to the canary (this can be adjusted over time - or the canary can be promoted to make it the new base stage)

### 😡 Errors

#### 4XX - Client Error - Invalid request on client side

- `400` - Bad Request - Generic
- `403` - Access denied (authorizer denies, WAF filtered)
- `429` - API Gateway can throttle (too many requests)

#### 5XX - Server Error - Valid request, backend issue

- `502` - Bad Gateway Exception (bad output returned by lambda)
- `503` - Service Unavailable (server might be down)
- `504` - Gateway Timeout (29s limit)

### 💾 Caching

Cache is defined per stage within API Gateway.

- TTL default is `300` seconds (configurable between `0` and `3600` second) 
- Can be **encrypted**
- Cache size from `500MB` to `237GB`
