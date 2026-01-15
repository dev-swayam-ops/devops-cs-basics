# 07 - DNS and HTTP Basics

## What You'll Learn

- DNS (Domain Name System) resolution
- DNS records (A, AAAA, MX, CNAME, TXT)
- DNS servers and recursive queries
- HTTP/HTTPS protocol basics
- HTTP methods (GET, POST, PUT, DELETE)
- Status codes (2xx, 3xx, 4xx, 5xx)
- Headers and request/response structure
- HTTPS and SSL/TLS certificates

## Prerequisites

- Completed **06-networking-fundamentals**
- Basic understanding of IP and ports
- Familiarity with domain names

## Key Concepts

### 1. DNS Resolution Process

DNS (Domain Name System) translates human-readable domain names into IP addresses. When you type a URL in your browser, DNS resolution happens transparently before any data is transferred.

**The Problem DNS Solves**
- Humans remember names (google.com), not numbers (142.251.32.14)
- Computers need IP addresses to route packets
- DNS bridges this gap - translates names to addresses
- Without DNS, web would be unusable - you'd have to memorize IP addresses

**DNS Resolution Flow**

**Step 1: Recursive Resolver (Your ISP)**
- Your computer queries the ISP's DNS resolver
- Resolver acts as an intermediary on your behalf
- Searches the entire DNS system if necessary
- Caches results for fast repeat lookups
- "Recursive" means it does the full job of finding the answer
- Most people don't interact directly with this - ISP provides it

**Step 2: Root Nameserver**
- Resolver queries one of 13 root nameservers (strategically placed worldwide)
- Roots don't know actual IP addresses, but know which TLD servers handle which domains
- Responds: "I don't know google.com, but ask the .com TLD server"
- Very fast response - just delegation, no lookup needed
- Same root servers used for all domain lookups

**Step 3: TLD (Top-Level Domain) Nameserver**
- Handles all domains with same TLD (.com, .org, .net, .edu, etc.)
- Resolver queries TLD server for the domain
- TLD responds: "I don't know google.com's IP, but ask the authoritative server at ns1.google.com"
- Another delegation - TLD server doesn't hold actual records
- Thousands of TLD servers, one set per TLD

**Step 4: Authoritative Nameserver**
- The actual server hosting the domain's DNS records
- Owner/admin of google.com maintains authoritative servers
- Responds with the actual IP address: 142.251.32.14
- This is the authoritative answer - the truth
- May be multiple authoritative servers for redundancy

**Step 5: Answer Returned**
- IP address returned to recursive resolver
- Resolver caches the answer (for efficiency on repeat queries)
- Answer sent back to your computer
- Browser now has IP address and can make HTTP request
- Entire process typically takes milliseconds

**Caching and TTL**
- Each DNS record has TTL (Time To Live) in seconds
- Resolver caches record for that duration
- After TTL expires, must query again for fresh answer
- Low TTL (300s): Quick propagation when records change, more queries
- High TTL (86400s): Fewer queries, slower to propagate changes
- Trade-off between freshness and efficiency

**Why Multiple Levels?**
- Distributes load - no single server handles all DNS
- Redundancy - failure of one server doesn't break DNS
- Scalability - millions of queries per second possible
- Hierarchy - root → TLD → authoritative is natural delegation

### 2. DNS Record Types

DNS records store different types of information about a domain. Understanding each type is essential for domain administration.

**A Record (Address Record)**
- Maps domain name to IPv4 address
- Most common record type
- Example: `google.com A 142.251.32.14`
- One domain can have multiple A records (load balancing)
- Queried by: `dig google.com A`

**AAAA Record (IPv6 Address)**
- Maps domain name to IPv6 address (128-bit)
- Becoming more common as IPv6 adoption increases
- Example: `google.com AAAA 2607:f8b0:4004:80c::200e`
- Allows accessing domain via IPv6
- Clients try AAAA first, fall back to A if not available
- Queried by: `dig google.com AAAA`

**CNAME Record (Canonical Name)**
- Creates alias - points one domain to another
- Example: `www.example.com CNAME example.com`
- Browser requests www.example.com → redirected to example.com → gets IP
- Cannot use CNAME at root level (conflicts with SOA record)
- Useful for: CDNs, subdomains, domain migrations
- Creates redirect at DNS level (different from HTTP redirect)
- Queried by: `dig www.example.com CNAME`

**MX Record (Mail Exchange)**
- Specifies email servers for the domain
- Priority value indicates preference (lower = higher priority)
- Example: `example.com MX 10 mail.example.com`
- When you send email to user@example.com, mail server looks up MX records
- Can have multiple MX records for redundancy
- Separate from A records - domain can have website on different server than email
- Queried by: `dig example.com MX`

**TXT Record (Text)**
- Stores arbitrary text information
- Used for multiple purposes:
  - **SPF (Sender Policy Framework)**: Specifies which servers can send email for domain
  - **DKIM**: Digital signatures to prevent email forgery
  - **DMARC**: Policy for handling email authentication failures
  - **Domain verification**: Services verify you own domain
- Example: `example.com TXT "v=spf1 include:_spf.google.com ~all"`
- Critical for email security and domain verification
- Queried by: `dig example.com TXT`

**NS Record (Nameserver)**
- Specifies which servers are authoritative for the domain
- Usually set at domain registrar
- Example: `example.com NS ns1.example.com`
- At least 2 NS records for redundancy
- Tells system "ask these servers for authoritative answers"
- Root delegates to TLD using NS records
- TLD delegates to authoritative using NS records
- Queried by: `dig example.com NS`

**SOA Record (Start of Authority)**
- Administrative information about the domain
- Contains: primary nameserver, administrator email, version, refresh/retry/expire times
- Used by DNS slaves to know when to sync from master
- One per domain zone
- Defines DNS zone boundaries
- Queried by: `dig example.com SOA`

**SRV Record (Service)**
- Maps service to server and port
- Example: `_ldap._tcp.example.com SRV 0 100 389 ldap.example.com`
- Used for: Kerberos, LDAP, SIP, etc.
- Specifies priority, weight, port, and hostname
- Application-specific - not commonly used on web

### 3. HTTP Request/Response Structure

HTTP (Hypertext Transfer Protocol) is the foundation of web communication. Understanding its structure is essential for web development and troubleshooting.

**HTTP Request Structure**

**Request Line**
- Specifies method, resource path, and HTTP version
- Example: `GET /api/users HTTP/1.1`
- Format: `METHOD /path HTTP/version`
- Method: GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS
- Path: URL path relative to domain
- Version: HTTP/1.0, HTTP/1.1, HTTP/2, HTTP/3

**Headers**
- Key-value pairs providing metadata about the request
- Separate from request line by blank line
- Common headers:
  - `Host: example.com` - domain being accessed (required)
  - `User-Agent: Mozilla/5.0` - what software is making request
  - `Content-Type: application/json` - format of request body
  - `Content-Length: 1234` - size of request body
  - `Accept: application/json, text/html` - what formats client understands
  - `Authorization: Bearer token123` - credentials
  - `Cookie: session=abc123` - stored session data
  - `Referer: https://google.com` - where request came from
- Case-insensitive but convention uses Title-Case
- Server can ignore unknown headers

**Body**
- Optional data sent with request
- Empty for GET requests
- Contains data for POST, PUT, PATCH (form data, JSON, XML, etc.)
- Size must match Content-Length header
- Format specified by Content-Type header

**Complete Request Example**
```
GET /api/users HTTP/1.1
Host: api.example.com
User-Agent: curl/7.68.0
Accept: application/json
Authorization: Bearer eyJhbGc...

(empty body for GET)
```

**HTTP Response Structure**

**Status Line**
- First line indicates success/failure
- Example: `HTTP/1.1 200 OK`
- Format: `HTTP/version status_code status_text`
- Status code: 3-digit number (200, 404, 500, etc.)
- Status text: Human-readable description

**Headers**
- Metadata about response
- Similar to request headers but different purposes
- Common response headers:
  - `Content-Type: application/json` - format of response body
  - `Content-Length: 5678` - size of response
  - `Set-Cookie: session=xyz789` - store session on client
  - `Cache-Control: max-age=3600` - how long to cache this response
  - `Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT` - when resource changed
  - `ETag: "abc123"` - unique identifier for this version
  - `Location: /new-path` - redirect to different URL
  - `Access-Control-Allow-Origin: *` - CORS permission

**Body**
- The actual data: HTML, JSON, images, files, etc.
- Size in Content-Length header
- Format in Content-Type header
- Can be empty (e.g., 204 No Content)

**Complete Response Example**
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 42
Cache-Control: max-age=3600

{"users": [{"id": 1, "name": "Alice"}]}
```

### 4. HTTP Status Codes

Status codes communicate the outcome of the request. They're essential for understanding what happened and how to handle the response.

**2xx Success (Request Succeeded)**
- `200 OK` - Request succeeded, response contains data (most common)
- `201 Created` - New resource created (POST usually returns this)
- `202 Accepted` - Request accepted for processing (async operations)
- `204 No Content` - Request succeeded but no content to return (DELETE usually returns this)
- `206 Partial Content` - Only part of resource returned (large file downloads)

**3xx Redirect (Client Must Take Additional Action)**
- `301 Moved Permanently` - Resource moved to new URL permanently; update bookmarks
- `302 Found` - Temporary redirect; try same URL next time
- `304 Not Modified` - Resource unchanged since last request; use cached version
- `307 Temporary Redirect` - Like 302, but client must use same method (POST stays POST)
- `308 Permanent Redirect` - Like 301, but method preservation like 307

**4xx Client Error (Client Did Something Wrong)**
- `400 Bad Request` - Request malformed; server cannot understand it
- `401 Unauthorized` - Authentication required; no valid credentials provided
- `403 Forbidden` - Authenticated but not allowed to access resource
- `404 Not Found` - Resource doesn't exist; URL is wrong
- `405 Method Not Allowed` - HTTP method not supported for this resource
- `409 Conflict` - Request conflicts with existing state (duplicate entry, etc.)
- `429 Too Many Requests` - Rate limited; too many requests too quickly

**5xx Server Error (Server Failed)**
- `500 Internal Server Error` - Unexpected server error; check logs
- `501 Not Implemented` - Server doesn't support requested feature
- `502 Bad Gateway` - Upstream server returned invalid response
- `503 Service Unavailable` - Server temporarily unavailable (maintenance, overloaded)
- `504 Gateway Timeout` - Upstream server took too long to respond

**Why Status Codes Matter**
- Debugging: tells you what went wrong
- Caching: 2xx cached, 3xx special handling, 4xx not cached, 5xx retry later
- Authentication: 401 triggers login flow, 403 shows permission error
- Error handling: applications handle different codes differently
- Monitoring: 5xx codes indicate problems requiring attention

### 5. HTTPS and SSL/TLS Certificates

HTTPS adds encryption and authentication to HTTP, protecting data from eavesdropping and tampering.

**Why HTTPS Matters**
- HTTP is plaintext - anyone on network can read it
- Password, credit cards, personal data sent in HTTP is visible to attackers
- ISP, Wi-Fi owner, network admin could see your data
- HTTPS encrypts everything - only server can decrypt
- Modern browsers mark HTTP sites as "not secure"

**How HTTPS Works (Simplified)**
- Client connects to server on port 443 (instead of 80)
- Server presents certificate (proves it's who it claims)
- Client verifies certificate is valid and trusted
- Both establish encryption key (handshake)
- All subsequent data encrypted with that key
- Only client and server know key - even ISP sees gibberish

**SSL/TLS Versions**
- SSL (Secure Sockets Layer): Original, deprecated (versions 2.0, 3.0)
- TLS (Transport Layer Security): Successor, current standard
- TLS 1.2: Widely supported, secure
- TLS 1.3: Latest, faster and more secure (fewer round trips)
- Most systems support both TLS 1.2 and 1.3
- Older versions disabled because vulnerabilities discovered

**Certificates**
- Issued by Certificate Authority (CA) - trusted third party
- Proves you own the domain
- Contains: domain name, expiration date, public key, CA signature
- CA signs certificate - clients trust CAs, therefore trust your certificate
- Self-signed: you sign your own (browsers show warning, use only for testing)
- Wildcard: `*.example.com` works for `api.example.com`, `www.example.com`, etc.
- Duration: usually 1 year, must be renewed
- Cost: free (Let's Encrypt) to hundreds (premium CAs)

**Certificate Chain**
- Your cert signed by intermediate CA
- Intermediate CA signed by root CA
- Root CA signed by itself (self-signed, built into browsers)
- Browser verifies chain: your cert → intermediate → root
- Allows trusting you without hardcoding every certificate

**Key Encryption Concepts**
- **Public Key**: Shared with everyone; encrypts messages for you
- **Private Key**: Secret; decrypts messages encrypted with public key
- Certificate contains public key
- Only you have private key - you decrypt data, prove you're you
- Asymmetric: public key cannot decrypt messages it encrypts

**Port 443**
- Standard HTTPS port (HTTP uses 80)
- Enables multiple secure services on different ports
- `https://example.com` defaults to port 443
- Certificate must match domain - `example.com` cert doesn't work for `www.example.com` (unless wildcard)

**Common Issues**
- **Expired certificate**: Browser shows warning, connection refused
- **Certificate mismatch**: Accessed `example.com` but cert says `other.com`
- **Self-signed**: Used for testing, browser shows warning but connection works
- **Mixed content**: HTTPS page loading HTTP content - browser blocks it
- **Weak encryption**: Outdated TLS version, weak cipher suite

## Hands-on Lab: DNS and HTTP Testing

### Lab Overview
Query DNS records and test HTTP requests.

### Lab Commands

```bash
# 1. Simple DNS lookup
nslookup google.com

# Expected:
# Name: google.com
# Address: 142.251.32.14

# 2. Query specific DNS server
nslookup google.com 8.8.8.8

# Expected: (same or different, tests resolver)

# 3. Query A record
dig google.com A

# Expected:
# google.com. 300 IN A 142.251.32.14

# 4. Query MX record
dig google.com MX

# Expected:
# google.com. 300 IN MX 10 smtp.google.com.

# 5. Query all records
dig google.com ANY

# Expected: (A, AAAA, MX, NS, etc)

# 6. Check DNS propagation
dig example.com +short

# Expected: 93.184.216.34

# 7. Test HTTP request
curl -I https://example.com

# Expected:
# HTTP/1.1 200 OK
# Content-Type: text/html
# Content-Length: 1256

# 8. Get full response
curl -s https://example.com | head -20

# Expected: (HTML page)

# 9. Post request
curl -X POST -H "Content-Type: application/json" \
  -d '{"name":"test"}' https://httpbin.org/post

# Expected: (echoed data)

# 10. Check certificate
openssl s_client -connect google.com:443

# Expected: (certificate details, issuer, expiry)
```

## Validation

```bash
# Can you query DNS?
nslookup google.com && echo "✓ DNS works"

# Understand A records?
dig google.com A && echo "✓ A records shown"

# Test HTTP?
curl -I https://example.com && echo "✓ HTTP works"

# Know status codes?
echo "✓ Status codes understood"
```

## Cleanup

No cleanup needed (read-only commands).

## Common Mistakes

1. **DNS vs IP**: DNS is name resolution, IP is address
2. **Port 80 vs 443**: 80 is HTTP, 443 is HTTPS
3. **HTTP methods**: GET is read, POST is write
4. **Status codes**: 3xx and 4xx are different (redirect vs error)
5. **Certificate validation**: HTTPS needs valid certificate

## Troubleshooting

| Problem | Solution |
|---------|----------|
| DNS not resolving | Check: nslookup, try different server (8.8.8.8) |
| Slow DNS | Try: faster resolver (Cloudflare 1.1.1.1) |
| HTTP timeout | Check: connectivity (ping), server status |
| Certificate error | Verify: domain name, certificate validity |
| Wrong IP | Check: A record, CNAME target |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **08-linux-kernel-basics** after completion

## Additional Resources

- DNS: `man nslookup`, `man dig`, `man host`
- HTTP: `man curl`, `man wget`
- Certificates: `man openssl`, `man s_client`

