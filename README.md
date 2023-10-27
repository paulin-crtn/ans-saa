# 🪣 Simple Storage Service

An object is identified by its `Key`.

## 👮‍♂️ Security & Policies

S3 is private by default (only accessible by the account root user of the account which created it). 
Policies can be used to allow access to buckets and objects:

- **Identity policies** (what that identity can access): limited because identity policies can only be attached to identities in **own** account
- **Resource policies** (who can access that resource): can ALLOW/DENY same or different account and anonymous principal
- **Access Control List**: limited and deprecated
- **Block Public Access**: safeguard for public bucket

```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Principal": "*",
         "Effect":"Allow",
         "Action":[
            "s3:GetObject",
            "s3:GetObjectVersion"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
      }
   ]
}
```

> [!NOTE]
> If the is a `Principal` it should be a Resource Policy, if not it should be an Identity policy

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

`s3:PutObject` allows upload up to **5GB** with a limit of 1 stream which is not optimal in terms of speed and reliability (if the stream fails then all the upload fails).

### 🍕 Multipart Upload

- Minimum data size of `100MB`
- Data is broken up: max `10,000` parts between `5MB-5GB` (last part can be smaller)
- Parts can fail and be restarted
- Better transfer rate (speeds of all parts)

### ⚡ Transfer Acceleration

Uses the AWS Edge Location network: instead of uploading data to a bucket (via the public network), it goes to the nearest Edge Location (via the public network) and then it is transferred to the bucket (via the AWS network).

> [!WARNING]
> Limitation to enable it: the bucket name cannot contains period and it needs to be DNS compatible in its naming

## 🙈 S3 Object Encryption

- Buckets are not encrypted, objects are
- Encryption in transit happens when transfering data between Users/App and an S3 Endpoint (independently of CSE or SSE)
- Encryption at rest means encryption where the data is stored (S3 Storage)

### 1️⃣ Client-Side Encryption

Customer does the encryption before sending data to the S3 Endpoint. AWS cannot see the data.

### 2️⃣ Server-Side Encryption

AWS handles the encryption, from the S3 Endpoint to the S3 Storage (now mandatory).

#### 🔒 Customer-Provided Keys (SSE-C)

Customer provides the plain text object and the key, then AWS does the encryption and discard the key.

#### 🔒 Amazon S3-Managed Keys (SSE-S3) - *this is the default*

Customer only provides the plain text objecy, then AWS does the encryption with a unique key that is also encrypted and stored with the object. Plain text key is discarded and cipher key can only be decrypted with a S3 managed master key.

There is some limitation because anyone with an administrator role will be able to access the decrypted object (not suitable for finance or other regulated sectors).

#### 🔒 KMS Keys Stored (SSE-KMS)

Customer create and manage a key within KMS. AWS does the encryption with the provided key that is also encrypted and stored with the object.

Isolated permissions are configurable 🙌

> [!NOTE]
> AWS KMS has a throttling limitation, so to use SSE-KMS with a large volume of upload the solution is to generate a time limited bucket key in KMS that will be used to generate DEKs within S3 (reduces KMS API calls, reducing cost and increasing scalability)

## 🌎 Static Website Hosting

It is possible to host HTML documents to a S3 bucket, turning S3 into a static website host. The main file, most likely `index.html`, will be accessible via an endpoint (the address depends on the region and bucket name). Custom domain is doable with R53, but only if the bucket name matches the domain name.

S3 hosting can be useful for:

- Offloading (static resource like images or videos will be in a bucket instead of a compute service)
- Out-of-band pages (in case of maintenance or outage of the server, we can update the DNS to redirect to a static page)

## 💶 Pricing

The following are charged:

1. **Storage** (prices depends on the selected storage class)
2. **Data transfer**: 📤 Transfer OUT (from S3 to internet) - *NB: 📥 Transfer IN (to S3 from internet) is always free*
3. **Request**: lifecycle transition, data retrieval, POST, PUT, GET, etc.

## 🏢 Storage Classes

### 📦 Standard

Objects are replicated across at least 3 AZs in the AWS region (`99.999999999%` durability).

- Millisecond first byte latency
- Objects can be made publicaly available
- No specific retrieval fee, no minimum duration, no minimum size

> [!NOTE]
> Useful for **frequently** accessed data which is **important** and **non replaceable**

### 📦 Standard-IA

Same as Standard class but with retrieval fee and a minimum duration charge of `30` days (objects can be stored for less but the minimum billing always applies) + minimum capacity charge of `128KB` per object.

> [!NOTE]
> Useful for **long lived** data which is **important** and **non replaceable** but where access is **infrequent**

### 📦 Standard One Zone-IA

Same as Standard-IA class but objects are only stored in **1 AZ** in the region (not as resilient).

> [!NOTE]
> Useful for **long lived** data which is **NON-CRITICAL** & **REPLACEABLE** and where access is **infrequent**

### 🧊 Glacier - Instant

Same as Standard-IA class but with more expensive retrieval fee and a minimum duration charge of `90` days.

> [!NOTE]
> Useful for **long lived** data accessed **once per quarter** with **millisecond** access

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
- Objects have to stay at least `30` days in S3 `Standard` before transitionning to `Standard-IA` or `One Zone-IA`
- Objects have to stay at least `30` days in `Standard-IA` or `One Zone-IA` before transitionning to `Glacier` classes (only if using the same rule 🤷‍♂️)

> [!WARNING]
> Careful with small object because they can cost more (minimum size of `128KB` or `40KB` depending on the class)


### 🚮 Expiration actions

Delete automatically an object.


# 🔑 Key Management Service

Create, store and manage symmetric and asymmetric keys in order to achieve cryptographic operations (encrypt and decrypt).

> [!IMPORTANT]
> **Keys never leave KMS** - Provides FIPS 140-2 (L2)

- KMS Keys are **logical** (ID, date, policy, desc & state) and backed by **physical** key material
- Keys are generated or imported in KMS and stored in an encrypted form (never in plain text)
- AWS owned vs. Customer owned
- Support rotation
- Backing key (can still decrypt data with previous keys)
- Can be used for up to **4KB** of data

> [!NOTE]
> Keys are isolated to a region (but can be replicated to multi-region)

## 🔏 Data Encryption Keys (DEK)

- Works on data > 4KB
- KMS generate a Plaintext key (used to encrypt the data and then discarded) and a Ciphertext key (used to decrypt the data)
- Data is stored with encrypted key outside KMS
- Data and encrypted key must be sent to KMS for decryption (KMS will decrypt the encrypted key)

## 👮‍♂️ Security & Policies

Unlike other AWS services, KMS has to explicitly be told that keys trust the AWS account that there contain within.
