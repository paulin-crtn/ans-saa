# 🌐 Route 53

GLOBAL SERVICE, globally resilient. Accessible from the public internet & VPCs.

- There are `13` root servers managed by `12` large organizations
- `IANA` - Manages the DNS root zone
- `Registry` - Owns TLD domains and officialize ownership
- `Registrar` - Rent domains (e.g. Namecheap, GoDaddy...)

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

## 🛣 R53 Routing

### 🚙 Simple Routing

Supports 1 Record per name (e.g. `www`) and each Record can have multiple values (e.g. `1.2.3.3`, `1.2.3.4`, etc.).

- All values are returned in a random order
- Use Simple Routing when you want to route requests towards **one service** such as a web server
