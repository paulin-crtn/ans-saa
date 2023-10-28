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
