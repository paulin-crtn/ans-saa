# 🌐 Route 53

GLOBAL SERVICE, globally resilient. Accessible from the public internet & VPCs.

- There are `13` root servers managed by `12` large organizations
- `IANA` - Manages the DNS root zone
- `Registry` - Owns TLD domains and officialize ownership
- `Registrar` - Domains registration (e.g. Namecheap, GoDaddy...)

## 🏪 Register Domains

R53 checks if a domain is available, if so it:

- Creates a `zone file` called `hosted zone`: database which contains all the DNS information for a particular domain (Resource **RECORDS**)
- Creates a `name service` for this zone: servers which R53 creates and manages which are distributed globally - most of the time there are 4 of these `nameservers` for one individual zone
- Assigns the `hosted zone` onto these four managed `nameservers` (NS)
- Adds these `nameserver` records into the `zone file` of the registry (top level domain, e.g. `.org`)

## 📄 Hosted Zones

- Zone files in AWS
- Hosted on 4 managed name servers
- Public or private
- Stores records (recordsets)

> [!NOTE]
> Externally registered domains can point at R53 Public Zone

### 🕵 Private Hosted Zone

- Associated with VPCs and only accessible in those VPCs
- VPCs can use the R53 Resolver `VPC +2` Address as their DNS Resolver to connect to Public Zone
- Split-view (overlapping **public** & **private**) for PUBLIC and INTERNAL use with the same zone name

## 🔠 DNS Record type

### ⏺️ Nameserver

Record type which allow **delegation** to occur in DNS. 

For example the `.com` zone will have multiple **nameserver records** inside it for `amazon.com`. These nameserver records are how the `.com` delegation happens for `amazon.com` and they point at servers managed by the `amazon.com` team and these servers host the a `amazon.com` zone. Inside it are **DNS records** such a `www`.

### ⏺️ A and AAAA

Map host names (e.g. `www`) to IP addresses.

- `A` ==> `IPv4`
- `AAAA` ==> `IPv6`

### ⏺️ CNAME

Maps a name to another name (cannot point directly to an IP address), e.g. `www.catagram.io` -> `catagram.io`.

- CNAME is invalid for apex (naked domain), e.g. `catagram.io`
- Many AWS services use DNS Name (ELBs)
- With just CNAME `catagram.io` -> `ELB` would be invalid

#### ALIAS

Maps a Name to an AWS Resource.

- AWS only (not a DNS standard)
- Can be used both for apex (naked domain) and normal records
- Should be the same "Type" as what the record is pointing at (e.g. `A Alias`)

### ⏺️ MX

Find a mail server (SMTP) for a domain. Have a priority and a value.

- `MX 10 mail` - Same zone
- `MX 10 mail.other.domain.` - Fully Qualified Domain Name (there is a dot at the end): Same or outside zone

### ⏺️ TXT

Add arbitrary text to a domain in order to provide additional functionality (e.g. prove domain ownership).

## ⌛ Time To Live

Numeric value in seconds. 

It tells the `Resolver server` how long the DNS is valid, so it does not have to query it again for a given time (it's a cache). Useful to avoid multiple unnecessary query but can cause DNS failure (`Client` -> `Resolver` -> `Root` -> `.com` -> `amazon.com`).

> [!NOTE]
> If doing any work that involves changing any DNS records it is recommended to lower TTL value days or weeks in advance to limit caching issue

Getting a result from the authoritative source (e.g. `amazon.com`) is called an **Authoritative answer** (Non-Authoritative if it comes from the Resolver).

## 💖 Health Checks

Monitor the health and performance of your web applications, web servers, and other resources. Each health check that you create can monitor one of the following:

- The health of a specified resource, such as a web server
- The status of other health checks (calculated)
- The status of an Amazon CloudWatch alarm

Good to know:

- Separated from (but used by) records
- Health checkers are **located globally** and can check anything (just need an IP address 😊)
- Health checkers checks every `30s` (every 10s costs extra): `Healthy` or `Unhealthy`
- TCP, HTTP(S), HTTP(S) with String Matching (more accurate, checks more than the status code)

> [!NOTE]
> If `18%+` of health checkers report as **healthy**, the health check is healthy 🙌 (based on the average response time, if there is a response)

## 🛣 R53 Routing

### 🚙 Simple Routing

Supports 1 Record per name (e.g. `www`) and each Record can have multiple values (e.g. `1.2.3.3`, `1.2.3.4`, etc.). 

- All values are returned in a random order
- DO NOT support health check

🎒 Use it when you want to route requests towards **one service** such as a web server.

### 🚙 Failover Routing

Supports multiple Records with the same name (e.g. `www`): 

- If the target of the health check is **Healthy** the primary record is used
- If the target of the health check is **Unhealthy** the secondary record is used

> [!NOTE]
> A common architecture is to use failover for "out of band" failure/maintenance page for a service (e.g. EC2/S3)

🎒 Use it when you want to configure active/passive failover.

### 🚙 Multi Value Routing

Supports multiple Records with the same name (e.g. `www`).

- Up to 8 healthy records are returned (if more exist, 8 are randomly selected)
- Each record is independent and can have an associated health check
- Any records which fail health checks won't be returned when queried
- Client chooses and uses 1 value

🎒 Use it to improves availability (not a replacement for load balancing).

### 🚙 Weighted Routing

Supports multiple Records with the same name (e.g. `www`). 

- Specify a weight for each record
- Each record is returned based on its record weight vs total weight (e.g. 30%)
- If a chosen record is unhealthy, the process of selection is repeated until a healthy record is chosen
- If weight is 0, then the record is not returned (if every weight is set to 0, all records are equally returned)

🎒 Use it for simple load balancing or **testing new software versions**.

### 🚙 Latency Routing

Supports multiple Records with the same name (e.g. `www`) in each AWS Region.

- Specify a region for each record
- The record returned is the one which offers the lowest estimated latency & is healthy
- If a record is unhealthy, then the next lowest latency is returned to the client
- Use an IP lookup service + latency table under the hood (NOT REAL TIME)

🎒 Use it for **performance** and user experience optimisation.

### 🚙 Geolocation Routing

Supports multiple Records with the same name (e.g. `www`).

- Close to latency routing but records are tagged with location.
- Order: `states` (USA only) -> `country` -> `continent` -> `default` (or NO ANSWER)

> [!IMPORTANT]
> Geolocation doesn't return the closest records, only **RELEVANT** (location) records (it's NOT about proximity)

🎒 Use it for regional restriction, language specific content or load balancing across regional endpoints.

### 🚙 Geoproximity Routing

Aims to provide records which are as close to customers as possible.

- Close to latency routing but based on **distance**
- Records can be tagged with an AWS region (or lat & long coordinates if not AWS)
- **Bias** can be added to rules in order to **increase** or **decrease** a region size

## 🔁 Interoperability

R53 can do BOTH or either **Domain Registrar** or **Domain Hosting**.

> [!NOTE]
> If done separately, it will be necessary to pass the allocated Name Servers details to the Registrar (who handles the TLD registration) 

## 🥷🏻 DNSSEC

Attackers sometimes hijack traffic to internet endpoints such as web servers by intercepting DNS queries and returning their own IP addresses to DNS resolvers in place of the actual IP addresses for those endpoints. Users are then routed to the IP addresses provided by the attackers in the spoofed response, for example, to fake websites.

You can protect your domain from this type of attack, known as DNS spoofing or a man-in-the-middle attack, by configuring Domain Name System Security Extensions (DNSSEC), a protocol for securing DNS traffic.

🎒 Exams:

- Create KMS Public and Private `KSK` (Key Signing Key) in `us-east-1`
- R53 handles internally the creation of a `ZSK` (Zone Signing Key)
- DNSSEC validation can be enabled for VPCs: invalid results on DNSSEC enabled zones won't be returned
- Create CloudWatch Alarms for `DNSSECInternalFailure` and `DNSSECKeySigningKeysNeedingAction` 
