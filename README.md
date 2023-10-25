# 🪣 Simple Storage Service 
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


