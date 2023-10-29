# 🧙 Identity and Access Management

NO COST - GLOBAL SERVICE

- User: Identities which represent humans or applications that need access to the account
- Group: Collection of related users (e.g. development team, finance or HR)
- Role: Used by AWS Services or for granting external access to the account

Policy is a document (or object) that **ALLOW** or **DENY** access to AWS services when attached to a user, group or role.

> [!IMPORTANT]
> Always give the fewest permissions necessary: **Least Priviledge Principle**

- Authenticate: prove you are who you claim to be
- Authorize: allow or deny access to resources

## 🔑 Access Keys

Long term credentials to log in an AWS account, composed of an `Access Key ID` and a `Secret Access Key`.

- An IAM user only has 1 username and 1 password but can have multiple access keys
- Access keys can be created, deleted, made active or inactive
- After creation, AWS won't show again the `Secret Access Key` (store it in a safe place)

## 📝 Policies

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GetObjectAndVersion",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:GetObjectVersion"],
      "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
    }
  ]
}
```

> [!NOTE]
> If there is a `Principal` it should be a Resource Policy, if not it should be an Identity policy