# ⭐ Serverless - Lambda

Function as a Service (FaaS): **short running and focused piece of code**. Event-Driven service.

- Deployment package: code + configuration (up to `50MB` zipped and `250MB` unzipped)
- Functions are loaded and run in a **runtime** environment (e.g. Node 18.2) - Also referred to as an Execution Context
- Billed for the duration of function execution: `15min` MAX (900 seconds)
- Stateless: each time a lambda is invoked, it can be inside a brand-new environment
- Common uses: Serverless App, File Processing, Database Triggers, Serverless CRON, Realtime Stream Data Processing, Events, etc.

The environment has:

- Direct memory allocation: from `128MB` to `10240MB` (1MB steps)
- Indirect CPU allocation (depending on the memory allocation, e.g. 1vCPU for 1769MB)
- Disk space allocation: `512MB` (default) storage available as `/tmp` (up to 10240MB)

## 🖇 Networking

### 🐵 Public

- By default, lambda functions are given public networking
- They can access public AWS Services and the public internet
- Offers the best performance because no customer specific VPC networking is required

> [!IMPORTANT]
> Lambda functions have NO ACCESS to VPC based services unless public IPs are provided & security controls allow external access

### 🙈 Private

- Lambda can be configured to run in a VPC's subnet: obey all VPC networking rules 🙌
- **VPC Endpoints** can provide access to **public AWS services**
- **NatGW** or **Internet Gateway** are required for VPC Lambdas to access **internet resources**

> [!IMPORTANT]
> Because Lambdas are running in their own VPC (AWS Lambda Service VPC), network permissions is required to communicate with other VPC. Can be accomplished with Elastic Network Interfaces (ENI): `90s` initial setup (done immediately, no invocation delay).

## 👮‍ Security

### 📋 Execution Role

Security is controlled using execution roles: IAM roles assumed by the Lambda function which provides permissions to interact with other AWS products and services (e.g. RDS or S3).

### 📋 Resource Policy

Lambda resource policy controls what services and accounts can INVOKE lambda functions (e.g. SNS).

## 📗 Logs

- **Logs** from Lambda executions go to `CloudWatch Logs` (requires permissions via Execution Role)
- **Metrics** (invocation success/failure, retries, latency, etc.) are stored in `CloudWatch`
- Lambda can be integrated with `X-Ray` for **distributed tracing**

## 🔮 Invocation

### ➡️ Synchronous

- Lambda function is invoked, passing in data and **wait for response**
- Result (Success or Failure) **returned during the request**
- Errors or retries have to be handled **within the client**

### ➡️ Asynchronous

Typically used when AWS services invoke lambda functions (they don't wait for a response).

- Lambda is responsible for reprocessing in case of failure (between 0 and 2, configurable) - Handle the retry logic
- The Lambda function needs to be idempotent (reprocessing a result should have the same end state)
- Events can be sent to dead letter queues after repeated failed processing
- Lambda supports destinations (SQS, SNS, Lambda & EventBridge) where successful or failed events can be sent

### ➡️ Event Source mappings

Typically used on streams or queues which don't support event generation to invoke lambda (Kinesis, DynamoDB Streams, SQS).

- Event Source Mapping map data from a Source Batch to an Event Batch
- Lambda function can handle multiple Events within a Batch (just be careful with execution timeout)
- SQS Queues or SNS topics can be used for any discarded failed event batches

> [!IMPORTANT]
> Permissions from the lambda execution role are used by the event source mapping to interact with the event source

## 🔢 Versions

- A version is the **code** + **configuration** of the lambda function
- A version is **immutable** - it never changes once published & has its **own ARN**
- `$Latest` points at the latest version
- Aliases (DEV, STAGE, PROD) point at a version - can be changed

## ⏱️ Start up time

- **Cold start**: Full creation and configuration of an execution context (including code download) - 100ms
- **Warm start**: Invocation re-use previous execution context - 1-2ms
- Concurrent executions will use multiple (potentially new) contexts
- **Provisioned concurrency** can be used: create and keep X contexts warm and ready to use (improve start speeds)
