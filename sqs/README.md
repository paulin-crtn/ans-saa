# 🚌 Simple Queue Service

Public AWS Service. HA, Scalable (Region resilient) and Fully Managed.

- Great for **decoupling** applications, **asynchronous** communications, worker pools, batch for future processing, etc.
- 1 production group, 1 consumption group (not like Kinesis)
- Messages `<=256KB` in size
- ASGs can scale and Lambdas invoke based on queue length
- A single request can have `1-10` messages, up to a maximum total payload of `256KB`
- Each `64KB` chunk of a payload is billed as 1 request (for example, an API action with a 256 KB payload is billed as 4 requests)
- Poll a SQS Queue: **Short** (immediate but can bill 1 request for 0 message) or **Long** (`waitTimeSeconds`, up to `20s`)
- Access to a queue is based on identity policies or queue policies (only queue policy can allow access from external accounts)
- Encryption at rest (KMS) & in-transit

> [!NOTE]
> SNS and SQS Fanout: Message is added into an SNS Topic and multiple SQS subscribed to it

Received messages are **hidden** from the queue (`VisibilityTimeout`)... then either reappear (retry) or are explicitly deleted.

- Default is 30 seconds - between 0 second and 12 hours
- Set on Queue or Per-Message
- Used to allow automatic reprocessing

> [!NOTE]
> No persistence of messages after process, no window (not like Kinesis)

## 📦 FIFO

- Guarantee exactly once delivery: **duplicates are removed**
- Guarantee the order: First-In First-Out (great when we need to process in a sequential order)
- Up to `300` transactions per second, or `3,000` messages with batching (300 transactions of 10 messages) => Not great with scaling
- Must have `.fifo` suffix in order to be a valid FIFO queue

## 📦 Standard

- Guarantee **at least once delivery** (but could be more 😬)
- No guarantee on the order of that delivery (Best-Effort Ordering)
- More like multi-lane highways => Great for scaling because it's easy to add a lane (near unlimited Transaction Per Second)

## ⏳ Delay Queues

- A Delay queue has a `DelaySeconds` set (default is 0 seconds, up to 15 minutes)
- Messages added to the queue will be invisible for this delay (start off in an invisible state)
- `ReceiveMessage` operations won't return any messages
- Message timers allow a per-message invisibility to be set, overriding any queue setting (not supported on FIFO queues)

## ☠️ Dead-Letter Queues

Useful when a message processing fails multiple times (`ReceiveCount` is incremented each time)

- **Redrive Policy**: specifies the Source Queue, the DLQ and the conditions where messages will be moved from one to the other
- Define `maxReceiveCount`, when `ReceiveCount` > `maxReceiveCount` and message isn't deleted, it's moved to the DLQ
- Can be notified when a message goes to DLQ
- Can test or apply separate processing

> [!IMPORTANT]
> SQS queues have retention periods for messages (message is dropped after that)

[!IMPORTANT]
> Enqueue timestamp is set on the message the first time it is added to the Standard or FIFO queue (timestamp is not updated, and not related to DLQ). Retention period of a DLQ is generally longer than the source queue.
