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

- Need to create a Web `ACL` (Access Control List): controls if traffic is allowed or blocked
- An ACL is made up of `Rule Groups`, themselves made up of `Rules` (processed in order)
- Resource type: CloudFront or Regional Service
- Web ACL Capcity Units (`WCU`) - Default `1500` (can be increased by support ticket)
- WEBACL's are associated with AWS resources (e.g. API GW, CloudFront)
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
