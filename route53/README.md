# 🌐 Route 53

GLOBAL SERVICE, globally resilient. 

## 🏪 Register Domains

R53 checks if a domain is available, if so it:

- Creates a `zone file` called `hosted zone`: database which contains all of the DNS information for a particular domain (RECORDS)
- Creates a `name service` for this zone: servers which R53 creates and manages which are distributed globally - most of the time there are 4 of these `nameservers` for one individual zone
- Assigns the hosted `zone onto` these four managed `nameservers` (NS)
- Adds these `nameserver` records into the `zone file` of the registry (top level domain, e.g. `.org`)

## 📄 Host Zones

- Zone files in AWS
- Hosted on 4 managed name servers
- Can be private or private (linked to VPC(s))
- Stores records (recordsets)

## 🔠 DNS Record type

### ⏺️ Nameserver

Record type which allow delegation to occur in DNS. 

For example the `.com` zone will have multiple **nameserver records** inside it for `amazon.com`. These nameserver records are how the `.com` delegation happens for `amazon.com` and they point at servers managed by the `amazon.com` team and these servers host the a `amazon.com` zone. Inside it are **DNS records** such a `www`.

### ⏺️ A and AAAA

Map host names (e.g. `www`) to IP addresses.

- `A` ==> `IPv4`
- `AAAA` ==> `IPv6`

### ⏺️ CNAME

Host to host (cannot point directly to an IP address). It's like an alias.

### ⏺️ MX

Find a mail server (SMTP) for a domain. Have a priority and a value.

- `MX 10 mail` - Same zone
- `MX 10 mail.other.domain.` - Fully Qualified Domain Name (there is a dot at the end): Same or outside zone

### ⏺️ TXT

Add arbitrary text to a domain in order to provide additional functionality (e.g. prove domain ownership).

## ⌛ Time To Live

Numeric value in seconds. 

It tells the `Resolver server` how long the DNS is valid so it does not have to query it again for a given time (it's a cache). Useful to avoid multiple unecessery query but can cause DNS failure (`Client` -> `Resolver` -> `Root` -> `.com` -> `amazon.com`).

> [!NOTE]
> If doing any work that involves changing any DNS records it is recommended to lower TTL value days or weeks in advance to limit caching issue

Getting a result from the authoritative source (e.g. `amazon.com`) is called an **Authoritative answer** (Non-Authoritative if it comes from the Resolver).
