# 🧭 CloudWatch

Collects and manages operational data. In order to keep things separated, CloudWatch uses **namespace** (container for related data).

## 📊 Metrics

- Collection of related data point in a time ordered structure: CPU Usage, Network IN/OUT, Disk IO
- Datapoint contains a timestamp and a value
- Dimensions separate data-points for different things or perspectives within the same metric

> [!NOTE]
> `CloudWatch Agent` is required when data is outside AWS (e.g. on-premises or in another cloud)

## 📒 Logs

- Public Service (regional): usable from AWS or on-premises
- Store, Monitor and access logging data (timestamp + some data)
- AWS Integrations: EC2, VPC Flow Logs, Lambda, CloudTrail, R53, etc.
- Can generate metrics based on logs: metric filter
- Retention & permissions are defined on a log group

> [!NOTE]
> **Log Events** are contained in a **Log Stream**, and log streams are contained in a **Log Group**.

> [!NOTE]
> `CloudWatch Agent` is required when data is outside AWS (e.g. on-premises or in another cloud)

## 🔔 Events 

Delivers a near real-time stream of system events.

- If X happens (or at Y time(s)) then do Z
- Alarm is linked to a metric
- State can be: `INSUFFICIENT_DATA`, `OK` or `ALARM` (can trigger an SNS or action)

> [!NOTE]
> AWS encourage to use `EventBridge` instead

- A default Event bus for the account (the only one in `CloudWatch`)
