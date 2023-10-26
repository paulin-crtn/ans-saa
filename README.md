# 🪣 Simple Storage Service 

An object is identified by its `Key`.

## Security & Policies

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

## Object Versioning

Disabled by default.

- Possible to store multiple versions of an object in a bucket
- Operations which would modify objects generate a new version (space is consumed -and billed- by ALL versions)
- S3 associate an `id` to an object (`null` if versioning is disabled)
- Can retrieve a specific version of an object with the `id` (if no `id` is provided, S3 returns the last/current version)

> [!IMPORTANT]
> Once activated it will be impossible to deactivate it again (versioning can only be suspended)

### Delete

Deleting an object will add a delete marker (a special version of an object) which will hide all previous version of the object. The delete marker can be deleted to undelete/show all previous version of the object.

To hard delete an object, it is necessary to specify the id of the version. If the current version is deleted, then the N-1 version becomes the current version.

#### MFA Delete

- Enabled in versioning configuration
- MFA is required to change bucket versioning state
- MFA is required to delete versions
- MFA serial number + code passed with API calls

## Performance

`s3:PutObject` allows upload up to **5GB** with a limit of 1 stream which is not optimal in terms of speed and reliability (if the stream fails then all the upload fails).

### Multipart Upload

- Minimum data size of **100MB**
- Data is broken up: max **10,000 parts** between **5MB-5GB** (last part can be smaller)
- Parts can fail and be restarted
- Better transfer rate (speeds of all parts)

### Transfer Acceleration

Uses the AWS Edge Location network: instead of uploading data to a bucket (via the public network), it goes to the nearest Edge Location (via the public network) and then it is transferred to the bucket (via the AWS network).

> [!WARNING]
> Limitation to enable it: the bucket name cannot contains period and it needs to be DNS compatible in its naming

## Static Website Hosting

It is possible to host HTML documents to a S3 bucket, turning S3 into a static website host. The main file, most likely `index.html`, will be accessible via an endpoint (the address depends on the region and bucket name). Custom domain is doable with R53, but only if the bucket name matches the domain name.

S3 hosting can be useful for:

- Offloading (static resource like images or videos will be in a bucket instead of a compute service)
- Out-of-band pages (in case of maintenance or outage of the server, we can update the DNS to redirect to a static page)

## Pricing

1. **Storage**: see S3 Object Storage Classes
2. **Data transfer**:
   - Transfer IN (to S3 from internet) is always free
   - Transfer OUT (from S3 to internet) is charged 
3. **Request**: lifecycle transition, data retrieval, POST, PUT, GET, etc. is charged


