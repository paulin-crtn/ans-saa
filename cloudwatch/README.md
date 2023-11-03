# 🧭 CloudWatch

Collects and manages operational data. In order to keep things separated, CloudWatch uses **namespace** (container for related data).

## 📊 CloudWatch Metrics

- Collection of related data point in a time ordered structure: CPU Usage, Network IN/OUT, Disk IO
- Datapoint contains a timestamp and a value
- Dimensions separate data-points for different things or perspectives within the same metric

> [!NOTE]
> `CloudWatch Agent` is required when data is outside AWS (e.g. on-premises or in another cloud)

## 📒 CloudWatch Logs

- Public Service (regional): usable from AWS or on-premises
- Store, Monitor and access logging data (timestamp + some data)
- AWS Integrations: EC2, VPC Flow Logs, Lambda, CloudTrail, R53, etc.
- Can generate metrics based on logs: metric filter
- Retention & permissions are defined on a log group

> [!NOTE]
> **Log Events** are contained in a **Log Stream**, and log streams are contained in a **Log Group**.

> [!NOTE]
> `CloudWatch Agent` is required when data is outside AWS (e.g. on-premises or in another cloud)

## 🔔 CloudWatch Events - EventBridge

Delivers a near real-time stream of system events.

- `CloudWatch Events` / `EventBridge` monitors events present in the `Event Bus`
- There is only one Event bus in `CloudWatch` (the default one) but EventBridge can have additional event buses
- Rules can be **SCHEDULED** or match an **EVENT PATTERN** and the event can be routed to one or many targets (e.g. Lambda)

> [!NOTE]
> AWS encourage to use `EventBridge` (same API but with more features)

- Alarms (linked to a metric) can be set - State can be: `INSUFFICIENT_DATA`, `OK` or `ALARM` (can trigger an SNS or action)
