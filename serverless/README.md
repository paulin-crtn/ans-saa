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
- **FANOUT**: one event (e.g. SNS) can initiate multiple workflow processes (e.g. SQS)
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

- Step Functions is based on state machines and tasks
- In Step Functions, a workflow is called a state machine, which is a series of **event-driven steps**
- Each step in a workflow is called a state
- A **Task** state represents a unit of work that another AWS service, such as AWS Lambda, performs
- A Task state can call any AWS service or API

> [!NOTE]
> Useful for serverless long-running workflows

## 🦋 Glue

Serverless `ETL` (Extract, Transform & Load) - not to be confused with `datapipeline` which can do ETL but uses servers (EMR)

- Crawls data sources and generates the AWS Glue **Data Catalog**
- Moves and **transforms** data between **source** and **destination**

### 📚 Data Catalog

The AWS Glue Data Catalog is an index to the location, schema, and runtime metrics of your data. You use the information in the Data Catalog to create and monitor your ETL jobs. Information in the Data Catalog is stored as metadata tables, where each table specifies a single data store.

- Avoids data silos: crawlers connect to data stores, determine schema and create metadata in the data catalog
- One catalog per region per account

### 👔 Glue Job

- **EXTRACT** from data source: 
  - Stores - S3, RDS, DynamoDB & JDBC Compatible
  - Streams - Kinesis Data Stream & Apache Kafka
- **TRANSFORM** data via a script
- **LOAD** data to targets: S3, RDS, JDBC Databases

> [!NOTE]
> Glue jobs can be initiated manually or via events using `EventBridge`

## ✉️ Amazon MQ

NOT A PUBLIC SERVICE - Private networking required (e.g. VPC)

- SNS and SQS are AWS services using AWS APIs
- SNS provides TOPICS (one-to-many communication channels)
- SQS provides QUEUES (one-to-one communication channels)

😓 Problem: Many organisations already use topics and queues with a different standard, so SQS and SNS won't work out of the box if the organisation wants to migrate to AWS

- Amazon MQ is an open-source message broker (provides QUEUES and TOPICS)
- Based on managed `Apache ActiveMQ`
- Suitable with JMS API or protocols such as AMQP, MQTT, OpenWire and STOMP
- Single Instance (Test, Dev, Cheap) or HA Pair (Active/Standby)
- No AWS native integration

## 🔄 AppFlow

Exchange data between applications (**connectors**) using **flows**.

- **Sync** data across applications (e.g. Slack to Redshift or Zendesk to S3)
- **Aggregate** data from different sources
- Public endpoints (but works with PrivateLink)
- **Connections** store configuration & credentials to access applications
- Connections can be reused across many flows (they are defined separately)
- Configure Source and Destination field Mapping
- Optional: data transformation, filters and validation
