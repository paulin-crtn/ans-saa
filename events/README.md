# SNS - SQS - Kinesis

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
