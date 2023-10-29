# 🔥 Amazon Web Services

## 🏘️ Accounts

- When creating an AWS Account, you provide an account name (e.g. PROD), a unique address email and a credit card
- An AWS Account is a container for `Identities` (users) and `Resources`
- By default all access to an AWS Account & resources is denied, except for the account root user
- The account root user has full control over all AWS Account and any resources created within it & cannot be restricted.
- IAM Users, Groups and Roles can be created and given FULL or LIMITED permissions

> [!NOTE]
> Use separate account for separate things (DEV, TEST, PROD) or products or clients

## 📟 Multi-Factor Authentication

Factor means a piece of evidence which prove identity, it can be based on:

- Knowledge: something you know (username, password)
- Possession: something you have (bank card, MFA device or app)
- Inherent: something you are (fingerprint, face, voice, iris)
- Location: a physical or network location

Because a factor can be leaked, more factors means more security and harder to fake !

## 🎎 Public vs Private Services

The difference is on a NETWORKING perspective.

1. Public Internet zone
2. AWS Public zone (S3, DDB, etc.)
3. AWS Private zone (VPC)

- Private services run in a VPC
- VPCs are isolated unless configured otherwise
- On-premises can access VPCs only if configured via `VPN` or `Direct Connect`
- VPC can access AWS Public zone or Public Internet zone via `Internet Gateway`

## 🌎 Global Infrastructure

### 🏜️ Regions

An area of ​​the world offering AWS services and consisting of at least 3 Availability Zones. Each AZ is composed of multiple data center. AZs are isolated from each other and connected with high speed, redundant networking (resilient **logical** -not physical- system).

- Geographic Separation - Isolated Fault Domain
- Geopolitical Separation - Different governance
- Location Control - Performance

> [!IMPORTANT]
> Some regions do not offer certain AWS services

Service Resilience:

- Globally Resilient: region can fail without impacting the service (e.g. IAM or S3)
- Region Resilient: AZ can fail without impacting the service
- AZ Resilient: Prone to failure despite some backup configuration

### 🎯 Edge Locations

Local distribution points.

## 💪 HA vs FT vs DR

### 💯 High Availability

HA is about **maximizing system online time** (e.g. `99.999%` = `5.26 minutes` p/year downtime). It's about fast and automatic recovery of issues (not about preventing user disruption). It comes with a cost: sometimes HA needs redundent servers or infrastructure to be in place ready to switch customer over to, in the event of a disaster to minimize downtime.

### 🤒 Fault Tolerant

FT means that if a system has faults then it should **continue to operate properly though a failure** without impacting customers. Cost more and harder to implement.

> [!NOTE]
> We can take the example of a plane with a motor engine problem: bacause HA won't ever be 100%, it's better to have FT than HA 😅

### 📋 Disaster Recovery

Set of policies, tools and **procedures to enable the recovery** of infrastructure and systems: what to do when disaster occurs ? Without a plan, bad decisions are made based on shock, lack of sleep and fear of how to recover. DR is not just about the tech but about **knowledge** and processes.

> [!IMPORTANT]
> DO NOT store backups at the same location as your systems

## 🤝 Shared Responsability Model

### 👀 AWS 

RESPONSABILITY FOR SECURITY **OF** THE CLOUD: Regions, AZ, Edge Locations, Hardware (compute, storage, database), etc.

### 👀 Customer

RESPONSABILITY FOR SECURITY **IN** THE CLOUD: Encryption, App, IAM, OS configuration, customer data, etc.
