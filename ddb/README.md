# 🗄️ DynamoDB

## 📐 Architecture

**NoSQL** Public Database-as-a-Service - Wide column **Key-Value** database

- No self-managed servers or infrastructure
- Highly resilient across AZs and optionally global
- Really fast... single-digit milliseconds (SSD based)
- Backups, point-in-time recovery, encryption at rest
- Event-Driven integration (do things when data changes)
- Billed based on RCU, WCU, storage & features

### 🗂️ Table

- Group of **ITEMS** with the same **PRIMARY KEY** (no limit for number of items in a table)
- PRIMARY KEY must be unique
- PRIMARY KEY: **Simple** (Partition Key) or **Composite** (Partition & Sort Key)
- ITEM MAX `400KB`
- No rigid attribute schema

#### 🏎️ Capacity

Speed of reads and writes. Set on a table.

- Writes: `1WCU` = `1KB` per second
- Reads: `1RCU` = `4KB` per second

#### 💾 Backups

##### 1. On-Demand Backups

- Fully Copy of Table (retained until removed)
- Restore: Same or Cross-Region, With or Without Indexes, Adjust Encryption Settings
- You're responsible for the backup

##### 2. Point-in-time Recovery

- Continuous record of changes allows replay to any point in the window
- `35-days` recovery window
- Not enabled by default

## 👨🏻‍🏭 Operations & Performance

### ✍️ Reading & Writing

- **On-Demand**: Unknown, unpredictable, low admin (more expensive)
- **Provisioned**: RCU & WCU set on a per-table basis (cheaper)
- Every operation consumes at least 1 RCU/WCU (rounds up)
- Every table has a RCU & WCU **burst pool** of `300 seconds`

### 🔍 Query

- Accepts ONLY a single `PK` value (and optionally an `SK`)
- Capacity consumed is the size of all returned items
- Further filtering discards data (capacity is still consumed)

### 🔦 Scan

- Any attributes can be used and any filters applied
- Moves through a table **consuming the capacity of EVERY ITEM** (including items not returned)
- Least efficient operation but the most flexible

## 🏋️ Consistency

When a data is written to the database and then immediately read, is that data immediately the same as the recent update ?

- Items are replicated in different `Storage Node` (1 per AZ)
- 1 Storage Node is elected as the Leader
- Writes are always direct to the **Leader Storage Node**
- The Leader Storage Node replicates data to other nodes (within a few milliseconds)

### 🤷 Eventually Consistent

- Reads check 1/3 nodes: Could be unlucky with **stale data** if a node is checked **before replication completes**
- Cheaper, easier to implement and scales better

### 💪 Strongly Consistent

- Reads **connect to the leader node** to get the **most up-to-date copy of data**
- Cost more, harder to implement and harder to scale

## 📍 LSI & GSI

- Indexes are **alternative views** on table data (alternative access pattern)
- Can use Query with a different `SK` (LSI) or a different `PK` & `SK` (GSI)
- Projected Attributes: `ALL`, `KEYS_ONLY` or `INCLUDE` (can choose which attributes are projected into the index)
- Use GSIs as default, LSI only when strong consistency is required

### #️⃣ Local Secondary Index

- **MUST** be created with a table
- Max `5` LSI per base table
- Alternative `SK` on the table (same PK)
- Shares the RCU and WCU with the table
- Indexes are **sparse**: only items which have a value in the index alternative SK are added to the index

### #️⃣ Global Secondary Index

- Can be created at any time
- Max `20` GSI per base table
- Alternative `PK` and `SK`
- GSIs have their own RCU and WCU allocations
- Indexes are **sparse**: only items which have values in the new PK (and optional SK) are added

> [!IMPORTANT]
> GSI's are **always eventually consistent**, replication between base and GSI is **Asynchronous**

## 🔴 Streams

Time ordered list of ITEM CHANGES in a table.

- `24-hour` rolling window
- Enabled on a per-table basis
- Records `INSERTS`, `UPDATES` and `DELETES`
- Different **View Types** influence what is in the stream: `KEYS_ONLY`, `NEW_IMAGE`, `OLD_IMAGE`, `NEW_AND_OLD_IMAGES`

## 🔫 Triggers

Serverless Event-Driven Architecture: **STREAMS + LAMBDA**

- ITEM changes generate an event
- That event contains the data which changed
- An action is taken using that data

## 🌎 Global Tables

Provides **multi-master cross-region** REPLICATION.

- Tables are created in multiple regions and added to the same global table
- **Last writer wins** is used for conflict resolution
- Reads and Writes can occur to any region (generally sub-second replication between regions)
- Strongly consistent reads ONLY in the same region as writes
- Provides Global HA and Global DR

## ⚡ DynamoDB Accelerator

In-memory cache for DynamoDB (much faster reads, reduces costs). Private Service (VPC).

- DAX is accessed via an endpoint: **CACHE HITS** are returned in **MICROSECONDS** (MISSES in milliseconds) - Low Latency
- Less complexity for the app developer & tighter integration

#### Traditional Cache

1. Application checks cache for data: a **CACHE MISS** occurs if data isn't cached
2. Data is loaded from the database with a **separate operation** and SDK
3. Cache is updated with retrieved data (subsequent queries will load data from cache as a CACHE HIT)

#### DAX

1. Applications uses the DAX SDK and make a single call for the data which is returned by DAX
2. DAX either returned the data from its cache or retrieves it from the database and then caches it

## 📐 Architecture

- DAX is deployed in multiple AZ inside a VPC (HA 🙌)
- **ITEM CACHE** holds results of `GetItem` or `BatchGetItem`
- **QUERY CACHE** holds results of `Query` or `Scan` + **parameters used**
- **Write-Through** is supported: data is written to DDB then DAX
- Primary NODE (Writes) and Replicas (Read)
- Scale **UP** and Scale **OUT** (Bigger or More)

> [!IMPORTANT] > **DO NOT use DAX** for App that require **Strongly Consistent Reads** or **Heavy Writes**

## ⏱️ Time To Live

- Timestamp for automatic DELETE of ITEMS
- Enabled on a table for a specific attribute
- A Per-Partition process periodically runs, checking the current time (in seconds since epoch) to the value in the **TTL attribute**: ITEMS where the TTL attribute is older than the current time are set to **expire**
- Another per-partition background process scans for expired items and removes them from tables and indexes: a DELETE RECORD is added to streams if enabled
- Any DELETE operation caused by TTL are background system processes: don't impact table performance and not chargeable
- A stream of TTL deletions can be enabled (24-hour window)
