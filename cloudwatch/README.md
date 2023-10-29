# 🧭 CloudWatch

Collects and manages operational data.

- **Metrics**

Collection of related data point in a time ordered structure: CPU Usage, Network IN/OUT, Disk IO. Datapoint contains 2 things : a timestamp and a value. Dimensions separate datapoints for different things or perspectives within the same metric.

- **Logs**

In order to keep things separated, CloudWatch uses **namespace** (container for related data).

- **Events**

Alarm is linked to a metric. State can be: `INSUFFICIENT_DATA`, `OK` or `ALARM` (can trigger an SNS or action).

> [!NOTE]
> Metrics/Logs: `CloudWatch Agent` is required when data is outside AWS (e.g. on-premises or in another cloud)
