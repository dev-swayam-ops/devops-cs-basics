# 07 - DNS and HTTP Basics: Exercises & Solutions

## Exercises

### E1: DNS Query (Easy)
```bash
nslookup example.com
dig example.com
host example.com
```
What are the A/AAAA/CNAME records? How long is TTL?

### E2: HTTP Methods (Easy)
```bash
curl -X GET http://httpbin.org/get
curl -X POST -d '{"key":"value"}' http://httpbin.org/post
curl -X PUT http://httpbin.org/put
curl -X DELETE http://httpbin.org/delete
```
What's difference between POST and PUT? When to use each?

### E3: HTTP Status Codes (Easy)
Research and explain: 200, 301, 400, 403, 404, 500, 503

### E4: DNS Caching (Medium)
```bash
dig example.com +trace          # Full DNS path
dig example.com @8.8.8.8        # Query specific nameserver
systemctl restart systemd-resolved
```
How does TTL affect caching? What is cache poisoning?

### E5: HTTPS/TLS (Medium)
```bash
curl -vI https://example.com    # See certificate
openssl s_client -connect example.com:443
```
What is certificate chain? How does TLS handshake work?

### E6: DNS Zone Transfer (Medium)
```bash
dig @ns1.example.com example.com AXFR  # Zone transfer
nslookup -type=MX example.com
nslookup -type=NS example.com
```
What are MX records? What is DNS zone file?

### E7: Load Balancing (Medium)
Multiple A records for single domain. How does this balance load?

### E8: HTTP Caching Headers (Medium)
```bash
curl -I http://example.com | grep -i cache
curl -H "Cache-Control: no-cache" http://example.com
```
What's difference between no-cache and no-store? How does Etag work?

### E9: Content Delivery Network (Medium)
How CDNs use DNS to serve content from closest edge. Research CloudFront/Akamai.

### E10: DNS Security (Medium)
What is DNSSEC? Why is DNS spoofing dangerous? How to prevent?

---

## Quick Solutions

**Q1**: DNS translates domain names to IP addresses using hierarchical system (root → TLD → authoritative)

**Q2**: 
- GET: Retrieve data (safe, idempotent)
- POST: Create resource (creates new)
- PUT: Update resource (replaces)
- DELETE: Remove resource

**Q3**: 
- 200: OK
- 301: Moved Permanently
- 400: Bad Request
- 404: Not Found
- 500: Server Error
- 503: Service Unavailable

**Q4**: TTL (Time To Live) controls how long result cached. Lower TTL = faster changes but more queries.

**Q5**: TLS uses public-key cryptography: 1) ClientHello 2) ServerHello + certificate 3) Key exchange 4) ChangeCipherSpec

**Q6**: MX records direct email, NS records specify authoritative servers, SOA records zone metadata

**Q7**: Load balancing: Single domain, multiple IPs, resolver returns different one each query

**Q8**: Cache-Control headers: no-cache (validate), no-store (don't cache), max-age (seconds). Etag allows conditional requests.

---

## Cheatsheet

```bash
# DNS Queries
nslookup domain [server]           # Basic lookup
dig domain +short                  # Short output
dig domain +trace                  # Trace path
host -t MX domain                  # Mail servers
host -t NS domain                  # Name servers

# HTTP Testing
curl -I url                        # Headers only
curl -v url                        # Verbose (debug)
curl -H "Header: value" url        # Add header
curl -X METHOD url                 # Method
curl -d "data" url                 # POST data
curl -b cookie.txt url             # Use cookies
curl -L url                        # Follow redirects

# HTTPS/TLS
openssl s_client -connect host:443
openssl x509 -in cert.pem -text   # View certificate
openssl s_client -connect host:443 -tls1_2
```

## Quiz

**Q1**: What does DNS stand for?
A) **Domain Name System** B) Data Network Service C) Digital Naming Service

**Q2**: What layer does DNS operate on?
A) Transport  B) **Application** C) Network D) Physical

**Q3**: What is HTTP status 301?
A) Not Found  B) **Moved Permanently** C) Bad Request D) OK

**Q4**: What does HTTPS add to HTTP?
A) Speed  B) **Security/Encryption** C) Routing D) Compression

**Q5**: What is TTL?
A) Transfer Time Limit  B) **Time To Live** C) Total Traffic Load D) Terminal Time Limit

**Q6**: What does curl -I do?
A) Follows redirects  B) Uses insecure SSL  C) **Fetches headers only** D) Interactive mode

**Q7**: What is Etag used for?
A) Encryption  B) **Conditional requests/caching** C) Session tokens D) Compression

**Short Answer:**
- **Q8**: Difference between A and CNAME records? (A = IP, CNAME = alias)
- **Q9**: What is DNS spoofing? (Attacker redirects domain to fake IP)
- **Q10**: Design CDN system (Edge servers, GeoDNS, cache invalidation)

---
