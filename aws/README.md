# 🔥 Amazon Web Services

## 🏘️ Accounts

- When creating an AWS Account, you provide an account name (e.g. PROD), a unique address email and a credit card
- An AWS Account is a container for `Identities` and `Resources`
- By default, all access to an AWS Account & resources is denied, except for the account root user
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

An area of the world offering AWS services and consisting of at least 3 Availability Zones. Each AZ is composed of multiple data center. AZs are isolated from each other and connected with high speed, redundant networking (resilient **logical** -not physical- system).

- Geographic Separation - Isolated Fault Domain
- Geopolitical Separation - Different governance
- Location Control - Performance

> [!IMPORTANT]
> Some regions do not offer certain AWS services

Service Resilience:

- Globally Resilient: region can fail without impacting the service (e.g. IAM)
- Region Resilient: AZ can fail without impacting the service
- AZ Resilient: Prone to failure despite some backup configuration

### 🎯 Edge Locations

Local distribution points.

## 💪 HA vs FT vs DR

### 💯 High Availability

HA is about **maximizing system online time** (e.g. `99.999%` = `5.26 minutes` p/year downtime). It's about fast and automatic recovery of issues (not about preventing user disruption). It comes with a cost: sometimes HA needs redundant servers or infrastructure to be in place ready to switch customer over to, in the event of a disaster to minimize downtime.

### 🤒 Fault Tolerant

FT means that if a system has faults then it should **continue to operate properly though a failure** without impacting customers. Cost more and harder to implement.

> [!NOTE]
> We can take the example of a plane with a motor engine problem: because HA won't ever be 100%, it's better to have FT than HA 😅

### 📋 Disaster Recovery

Set of policies, tools and **procedures to enable the recovery** of infrastructure and systems: what to do when disaster occurs ? Without a plan, bad decisions are made based on shock, lack of sleep and fear of how to recover. DR is not just about the tech but about **knowledge** and processes.

> [!IMPORTANT]
> DO NOT store backups at the same location as your systems

## 🤝 Shared Responsibility Model

### 👀 AWS 

Responsibility for security **OF** the cloud: Regions, AZ, Edge Locations, Hardware (compute, storage, database), etc.

### 👀 Customer

Responsibility for security **IN** the cloud: Encryption, App, IAM, OS configuration, customer data, etc.

## 🏢 Organizations

For large business with multiple accounts it is recommended to create a **Management Account** from a **Standard Account** in order to manage easily all accounts. Accounts will need to accept the invitation from Management Account in order to be part of the organization. Standard account is now referenced as **Member Accounts** of that organization.

> [!NOTE]
> This will create an **Organization Root** that can contains none, one or many **Organizational Unit** (Organizational Unit can be nested)

> [!IMPORTANT]
> Management Account is the **Payer Account** of all accounts inside the organization: **Consolidated Billing** + Consolidation of reservations and volume discounts

- It is possible to create a Member Account from a Management Account (just need a valid unique email address) - No invite process 🙂
- Do not need Users inside every single AWS account: IAM roles can be used to allow IAM Users to access other AWS accounts (identities can be handled in the Management Account or a dedicated Member Account)

## 🛂 Service Control Policies

SCPs are **account permissions boundaries**: they limit what the account can do (so they are restricting what the account root user can allow even if the account root user cannot be directly restricted). 

- Do not grant permissions (still need to give identities) but can restrict what permission can be given or not
- **Allow list** (this is the list created by default with `fullAWSAccess`) vs **Deny list** (can be deleted so the implicit deny prevails, then add allow list when needed: less admin overhead)

> [!NOTE]
> Effective permissions for identities within an account are the overlap between any identity policies and any applicable SCPs

SCP can be attached to:

- **Organization Root**
- **Organizational Unit** (one or many): will impact all accounts or nested Organizational Unit within
- **Individual Account** (one or many)

> [!IMPORTANT]
> **Management Accounts** are never impacted by SCP (cannot be restricted): best practice to not use resources in Management Accounts

## 🕵🏼 CloudTrail

Monitor and log any API call or activities done in an AWS account by a user, a role or a service: `CloudTrail Event`.

- `90`days stored by default in `Event History` - Enabled by default & no cost for 90 day history
- **Management** Events (e.g. configuration of a resource) and **Data** Events (e.g. data accessed, inserted or updated)
- Only **Management** Events is enabled by default (because too much data for **Data** Events)
- A trail is way to provide a configuration to CloudTrail on how to operate: can be configured for One Region or All Regions
- Global Service Event: some services log events globally to one region (`us-east-1`) like `IAM`, `STS` or `CloudFront`, so CloudTrail needs to be configured to catch them
- Can create an **organizational trail**: create a trail from the **Management Account** of an organization will store all information of all accounts 🙌
- **Event logs** can be stored in **CloudWatch Logs** or indefinitely into an **S3** bucket (only charge for storage) 🙂

> [!IMPORTANT]
> NOT REALTIME - There is delay (within 15 minutes)
