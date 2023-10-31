# 🪣 Simple Storage Service

GLOBAL storage platform - **regional** based/resilient. Public service, unlimited data & multi-user. Great for large scale data storage, distribution or upload. Input and/or Output to many AWS products.

- Buckets are containers for objects and are identified by a **globally unique name**
- Bucket name must be `3-63` characters, all lower case and no underscores (start with a lower case or a number, cannot be IP formatted)
- Default limit of S3 buckets in an AWS account is `100` (soft limit) or `1000` (hard limit)
- Flat structure (UI can trick you to think otherwise)
- An object is identified by its `Key` (its name), has a value (content data) & some extra information (Version ID, Metadata, Access Control & Subresources)
- Object can have a size between 0 bytes and `5TB`
- Unlimited number of objects in a bucket

⚠️ S3 is an object store (not file or block: cannot mount an S3 bucket)

## 👮‍♂️ Security & Policies

S3 is private by default (only accessible by the account root user of the account which created it).
Policies can be used to allow access to buckets and objects:

- **Identity policies** (what that identity can access): limited because identity policies can only be attached to identities in **own** account
- **Resource policies** (who can access that resource): can ALLOW or DENY same or different account and anonymous Principal
- **Access Control List**: limited and deprecated
- **Block Public Access**: safeguard for public bucket

## 🗂️ Object Versioning

Disabled by default.

- Possible to store multiple versions of an object in a bucket
- Operations which would modify objects generate a new version (space is consumed -and billed- by ALL versions)
- S3 associate an `id` to an object (`null` if versioning is disabled)
- Can retrieve a specific version of an object with the `id` (if no `id` is provided, S3 returns the last/current version)

> [!IMPORTANT]
> Once activated it will be impossible to deactivate it again (versioning can only be suspended)

### 🗑️ Delete

Deleting an object will add a delete marker (a special version of an object) which will hide all previous version of the object. The delete marker can be deleted to undelete/show all previous version of the object.

To hard delete an object, it is necessary to specify the id of the version. If the current version is deleted, then the N-1 version becomes the current version.

#### 📟 MFA Delete

- Enabled in versioning configuration
- MFA is required to change bucket versioning state
- MFA is required to delete versions
- MFA serial number + code passed with API calls

## 🚀 Performance

`s3:PutObject` allows upload up to `5GB` with a limit of 1 stream which is not optimal in terms of speed and reliability (if the stream fails then all the upload fails).

### 🍕 Multipart Upload

- Minimum data size of `100MB`
- Data is broken up: max `10,000` parts between `5MB-5GB` (last part can be smaller)
- Parts can fail and be restarted
- Better transfer rate (speeds of all parts)

### ⚡ Transfer Acceleration

Uses the AWS Edge Location network: instead of uploading data to a bucket (via the public network), it goes to the nearest Edge Location (via the public network) and then it is transferred to the bucket (via the AWS network).

> [!WARNING]
> Limitation to enable it: the bucket name cannot contain period and it needs to be DNS compatible in its naming

## 🙈 S3 Object Encryption

- Buckets are not encrypted, objects are
- Encryption in transit happens when transferring data between Users/App and an S3 Endpoint (independently of CSE or SSE)
- Encryption at rest means encryption where the data is stored (S3 Storage)

### 1️⃣ Client-Side Encryption

Customer does the encryption before sending data to the S3 Endpoint. AWS cannot see the data.

### 2️⃣ Server-Side Encryption

AWS handles the encryption, from the S3 Endpoint to the S3 Storage (now mandatory).

#### 🔒 Customer-Provided Keys (SSE-C)

Customer provides the plain text object and the key, then AWS does the encryption and discard the key.

#### 🔒 Amazon S3-Managed Keys (SSE-S3) - _this is the default_

Customer only provides the plain text objecy, then AWS does the encryption with a unique key that is also encrypted and stored with the object. Plain text key is discarded and cipher key can only be decrypted with a S3 managed master key.

There is some limitation because anyone with an administrator role will be able to access the decrypted object (not suitable for finance or other regulated sectors).

`AES256` encryption.

#### 🔒 KMS Keys Stored (SSE-KMS)

Customer create and manage a key within KMS. AWS does the encryption with the provided key that is also encrypted and stored with the object.

Isolated permissions are configurable 🙌

> [!NOTE]
> AWS KMS has a throttling limitation, so to use SSE-KMS with a large volume of upload the solution is to generate a time limited bucket key in KMS that will be used to generate DEKs within S3 (reduces KMS API calls, reducing cost and increasing scalability)

## 🌎 Static Website Hosting

It is possible to host HTML documents to a S3 bucket, turning S3 into a static website host. The main file, most likely `index.html`, will be accessible via an endpoint (the address depends on the region and bucket name). Custom domain is doable with R53, but only if the bucket name matches the domain name. An error document is also required.

S3 hosting can be useful for:

- Offloading (static resource like images or videos will be in a bucket instead of a compute service)
- Out-of-band pages (in case of maintenance or outage of the server, we can update the DNS to redirect to a static page)

## 💶 Pricing

The following are charged:

1. **Storage** (prices depends on the selected storage class)
2. **Data transfer**: 📤 Transfer OUT (from S3 to internet) - _NB: 📥 Transfer IN (to S3 from internet) is always free_
3. **Request**: lifecycle transition, data retrieval, POST, PUT, GET, etc.

## 🏢 Storage Classes

### 📦 Standard

Objects are replicated across at least 3 AZs in the AWS region (`99.999999999%` durability).

- Millisecond first byte latency
- Objects can be made publicaly available
- No specific retrieval fee, no minimum duration, no minimum size

> [!NOTE]
> Useful for **frequently** accessed data which is **important** and **non-replaceable**

### 📦 Standard-IA

Same as Standard class but with retrieval fee and a minimum duration charge of `30` days (objects can be stored for less but the minimum billing always applies) + minimum capacity charge of `128KB` per object.

> [!NOTE]
> Useful for **long lived** data which is **important** and **non-replaceable** but where access is **infrequent**

### 📦 Standard One Zone-IA

Same as Standard-IA class but objects are only stored in **1 AZ** in the region (not as resilient).

> [!NOTE]
> Useful for **long lived** data which is **NON-CRITICAL** & **REPLACEABLE** and where access is **infrequent**

### 🧊 Glacier - Instant

Same as Standard-IA class but with more expensive retrieval fee and a minimum duration charge of `90` days.

> [!NOTE]
> Useful for **long-lived** data accessed **once per quarter** with **millisecond** access

### 🧊 Glacier - Flexible

Same as Glacier - Instant class but with a minimum capacity charge of `40KB` per object. Objects cannot be made publicly accessible: any access of data (beyond object metadata) requires a retrieval process. First bytes latency: minutes to hours (faster = more expensive).

- Expedited (1-5 minutes)
- Standard (3-5 hours)
- Bulk (5-12 hours)

> [!NOTE]
> Useful for **archival** data where frequent or realtime access isn't needed

### 🧊 Glacier - Deep Archive

Same as Glacier - Flexible class but with a minimum duration charge of `180` days. First bytes latency: hours to days.

- Standard (12 hours)
- Bulk (up to 48 hours)

> [!NOTE]
> Useful for **archival** data that rarely - if ever - needs to be accessed (e.g. legal or regulation data storage)

### 🤓 Intelligent-Tiering

Monitors and automatically moves any objects not accessed for 30 days to a low cost infrequent access tier and eventually to archive instant access, archive access or deep archive tiers. Cost per 1,000 objects.

> [!NOTE]
> Useful for **long-lived** with **changing** or **unknown** patterns

## 🕓 Lifecycle Configuration

Set of rules which consist of actions that apply to a bucket or groups of objects depending on some criteria (if X then do Y). Helps to reduce costs and keep buckets tidy.

### 🤿 Transition actions

- Move automatically objects between any storage classes (only down, not upward) - except for `One Zone IA` -> `Glacier Instant Retrieval` !
- Not based on how often an object is accessed (this is Intelligent-Tiering), but rather over a number of days
- Objects have to stay at least `30` days in S3 `Standard` before transitioning to `Standard-IA` or `One Zone-IA`
- Objects have to stay at least `30` days in `Standard-IA` or `One Zone-IA` before transitioning to `Glacier` classes (only if using the same rule 🤷‍♂️)

> [!WARNING]
> Careful with small object because they can cost more (minimum size of `128KB` or `40KB` depending on the class)

### 🚮 Expiration actions

Delete automatically an object.

## 💿 Replication

- Cross-Region Replication: useful for resilience improvements and latency reduction
- Same-Region Replication: useful for log aggregation, prod and test sync, resilience (strict sovereignty)

Replication can take place within the same account or to a different account (in this case the destination bucket will not trust the source and will need a policy).

🧰 Options:

- All objects or subset (based on a prefix)
- Storage class (default is to maintain)
- Ownership (default is the source account)
- Replication Time Control (RTC)

🎒 Exams:

- By default not retroactive & versioning needs to be on
- Batch replication can be used to replicate existing objects (must be specifically configured)
- One-way replication (source to destination) or bidirectional (new, must be specifically configured)
- Encryption: unencrypted, SSE-S3, SSE-KMS and SSE-C
- Source bucket owner needs permissions to objects
- No replication for system events, Glacier or Glacier Deep Archive objects
- No replication for delete (by default, but this can be added - `DeleteMarkerReplication`)

## 🖌️ Pre-signed URLs

Give to another person or application access to an S3 object inside a bucket using your credentials in a safe and secure way. Upload (`PUT`) or download (`GET`).

1. User request access to an object in a private bucket
2. Server generate a pre-signed URL (configure to expire at a certain time)
3. Server communicate the pre-signed URL to the user
4. User can access the object using the pre-signed URL

🎒 Exams:

- You can create a URL for an object you have no access to
- When using the URL, the permissions match the identity which generated it
- Access denied could mean the generating ID never had access... or doesn't now
- Not recommended to generate with a role but with an identity (IAM user) because URL stops working when temporary credentials expire

## 👌 Select and Glacier Select

Retrieves part of object rather than the entire object. Because object can be up to `5TB`, retrieving a large object takes time and consume all of its size. S3/Glacier Select lets us use SQL-Like statements to select part of the object, **pre-filtered** by S3 (instead of filtering on the client side, once all data have been transferred/consumed).

## 🎉 Event Notifications

Notification generated when events occur in a bucket can be delivered to `SNS`, `SQS` and `Lambda` functions (with the right Resource Policy in order to allow S3 principal access). Event notification is JSON object. Events can be any of the following:

- Object created
- Object delete
- Object restore
- Replication

> [!NOTE] 
> **EventBridge** is an alternative and supports more types of events and more services

## 📗 Logs

It is possible to log bucket and object access from a `Source Bucket` to a `Target Bucket` (configured with `ACL` to allow `S3 Log Delivery Group`). Log files consist of log records. Best efforts log delivery: accesses to Source Bucket are usually logged in Target Bucket within a few hours.

## 🤚 Object Lock

Can be enabled on new bucket (for existing one, contact AWS support) and requires versioning (individual versions are locked). Write-Once-Read-Many (`WORM`) - No Delete, No Overwrite.

### ⛓️ Retention Period

Specify days and years retention periods.

1. COMPLIANCE: cannot be adjusted, deleted or overwritten (not even by the account root user) until retention expires
2. GOVERNANCE: `s3:BypassGovernanceRetention` permission can be granted to an identty allowing lock settings to be adjusted (+ header `x-amz-bypass-governance-retention:true` along with the request)

### ⛓️ Legal Hold

NOT about setting a retention period.

- Set on an object **version**: ON or OFF
- No deletes or changes on object version until legal hold is removed (`s3:PutObjectLegalHold` is required to add or remove)
- Prevent accidental deletion of critical object versions

## 🧑‍💻 Access Points

Simplify managing access to S3 buckets and objects: rather than having 1 bucket with 1 policy we can create many access points, each with different policies, each with different network access controls. Useful for large bucket with multiple identities and complex policy.

Each access point has its own endpoint address created via the AWS Console or `aws s3control create-access-point`. Access Point policies control permission for access via the Access Point & is functionally equivalent to a bucket policy. Access Point policy can restrict identities to certain prefix(s), tags or actions based on need.

Each Access Point has a unique DNS address for network address. Access Points can be configured for access via VPC (requires a VPC endpoint).
