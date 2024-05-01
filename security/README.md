# 👮 Security

## 🤐 Secrets Manager

- It does share functionality with Parameter Store
- Designed for secrets: **passwords**, **API KEYS**, etc.
- Supports automatic **rotation** (uses Lambda)
- Directly integrates with some AWS products (e.g. `RDS`)
- Secrets are encrypted using `KMS`

## 🚧 Application Firewalls (Layer 7)

- Layer 3 and 4: REQUEST and RESPONSE are different and unrelated
- Layer 5: REQUEST and RESPONSE can be considered as part of one SESSION

Both can see IP addresses, ports, flags (+ sessions for Layer 5).
Neither of them can understand the data which flows over the top of this (no visibility of Layer 7, e.g. HTTP).

Layer 7 Firewalls is aware of the Layer 7 protocol (HTTP(S), DNS, Rate, Content, Headers), is able to identify, block & adjust specific applications (e.g. Facebook) & can identify normal or abnormal requests:

1. L7FW **decrypts** (terminates) the HTTPS request
2. then L7FW **inspects**, blocks, replaces or tags the request
3. then L7FW **encrypts** the request and pass it to the backend (if not blocked)

## 🔥 Web Application Firewall (WAF)

WAF provides protection against **Layer 7 attacks**, **SQL injection** and **Cross-Site Scripting**.

- Need to create a Web `ACL` (Access Control List): controls if traffic is allowed or blocked
- An ACL is made up of `Rule Groups`, themselves made up of `Rules` (processed in order)
- Resource type: CloudFront or Regional Service
- Web ACL Capcity Units (`WCU`) - Default `1500` (can be increased by support ticket)
- WEBACL's are associated with AWS resources: `API GW`, `CloudFront`, `ALB`, etc.
- ACL can be associated to many resources (but a resource can only have one ACL linked to it)
- $5/month per WEBACL, $1/month per rule and $0.60/1M requests
- Extra: intelligent threat migration, bot control, fraud control/account takeover

### 📁 Rule Groups

- Can be `Managed` (AWS or Marketplace), `Yours` or `Service Owned` (i.e. Shield & Firewall Manager)

### 📐 Rules

- **Type**: `Regular` or `Rate-based`
- **Statement**: `WHAT` to match, `COUNT`, WHAT & COUNT (origin country, IP, label, header, cookies, query params, HTTP method, URI path, body - **first 8192 bytes only**)
- Can have one or multiple segment (`AND`, `OR`, `NOT`), can use `REGEX`, starts with, ends with, contains, etc.
- **Action**: `Allow` (🙃 non-sense for rate-based), `Block`, Count, Captcha, Custom Response (`x-amzn-waf-`), Label (inside of WAF only)
- ALLOW & BLOCK stop processing, Count/Captcha actions continue (and Label canbe used)

## 🛡️ Shield

DDOS Protection.

- **Network Volumetrics** Attacks (Layer 3) - Saturate Capacity
- **Network Protocol** Attacks (Layer 4) - TCP SYN Flood (leave connections open, prevent new ones)
- **Application Layer** Attacks (Layer 7) - e.g. web requests floods (cheap to request but expensive to deliver the result, like requesting all pictures)

### 🔰 Shield Standard

- Free
- Protection at the perimeter: `Region/VPC` or `AWS Edge`
- `Common Network` (L3) or `Transport` (L4) layer attacks
- Best protection using R53, CloudFront, AWS Global Accelerator

### 🔰 Shield Advanced

- $3000 per month with 1 year commitment (+ charge for data OUT)
- Protects R53, CloudFront, AWS Global Accelerator, Elastics IPs (i.e. EC2), ALBs, CLBs, NLBs ➡️ Not automatic, must be explicitly enabled
- Cost protection (i.e. EC2 scaling) for unmitigated attacks
- Proactive Engagement & AWS Shield Response Team
- Real time visibility of DDOS events and attacks & Health-based detection
- `Application Layer` (L7) DDOS protection with `WAF` Integration
- Protection groups

## 🔐 CloudHSM

Manage **SINGLE-TENANT** `Hardware Security Modules` (HSMs) on AWS.

- AWS provisioned... fully customer managed (deployed inside a VPC)
- Generate and use cryptographic keys on dedicated `FIPS 140-2 Level 3` single-tenant HSM instances (KMS si L2 Overall, some L3)
- Industry Standard APIs - `PKCS#11`, `JCE` (Java Cryptography Extensions), Microsoft `CryptoNG` (CNG) librairies
- `KMS` can use `CloudHSM` as a custom key store
- **NO native AWS integration**

> [!NOTE]
> Use-cases: encrypt data at rest, offload SSL and TLS processing for web servers, protect private keys for an issuing certificate authority, activate TDE (transparent data encryption) for Oracle databases etc.

## ⚙️ Config

**Record configuration changes over time on resources**: who did what, on which resource, when.

- **Auditing** of changes, **compliance** with standards
- DOES NOT prevent changes happening: NO PROTECTION
- Regional service but supports cross-region and account aggregation
- Every time a change occurs on a resource a **Configuration Item** (`CI`) is created and stored in S3 (all CIs for a given resource is called a configuration history)
- Resources are evaluated against **Config Rules** - either AWS Managed or custom (using Lambda)
- Changes can generate `SNS` notifications and near-realtime event via `EventBridge` or `Lambda`

## 🪣 Macie

**Discover**, **Monitor** and **Protect** data stored in `S3` buckets.

- **Automated discovery of data** (e.g. identity, finance or credentials data) ➡️ `Managed Data Identifiers` (Built-in: ML, Patterns) or `Custom Data Identifiers` (Proprietary: REGEX based)
- `Policy` findings (when policy on the bucket is changed in a way that reduces the security, e.g. enabling public on S3 bucket) or `Sensitive Data` findings
- Integrates with Security Hub & EventBridge
- Centrally managed: `AWS ORG` or one `Macie Account Inviting`

## 🕵️ Inspector

Automatically discovers workloads, such as Amazon EC2 instances, containers, and Lambda functions, and scans them for **software vulnerabilities**, **unintended network exposure** and deviations against **security best practices**.

- Length: 15 minutes, 1 hour, 8/12 hours or 1 day
- Rules packages determine what is checked
- Provides a report of findings ordered by priority
- Agent can provide additional OS visibility

🎒 **Exams**

- **Network Assessment** (Agentless)
- **Network & Host Assesment** (Agent)
- **Network Reachability** (no agent required)
- Common Vulnerabilities and Exposures (`CVE`)
- Center for Internet Security (`CIS`) benchmarks

## 📺 GuardDuty

Continuous security monitoring service.

Combines `ML` and integrated `threat intelligence` to help protect your AWS accounts, workloads, and data from **threats**: learns **pattern** about what happend normally.

- Identifies **unexpected** and **unauthorised** activity
- **Notify** or **Event-driven** protection/remediation
- Supports multiple accounts: MASTER and MEMBER
- Looks inside `DNS` logs, `VPC` Flow logs, `CloudTrail` Event, etc.
