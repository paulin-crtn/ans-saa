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


