# 07 - DNS and HTTP: Quiz

## MCQ (7 Questions)

**Q1**: What does DNS stand for?
A) **Domain Name System**  B) Data Network Service  C) Digital Naming Service  D) Domain Network Status

**Q2**: What layer does DNS operate on?
A) Transport  B) Network  C) **Application**  D) Physical

**Q3**: HTTP status code 301 means:
A) Not Found  B) **Moved Permanently**  C) Bad Request  D) OK

**Q4**: HTTPS adds what to HTTP?
A) Speed  B) **Encryption/Security**  C) Routing  D) Compression

**Q5**: TTL stands for:
A) Transfer Time Limit  B) **Time To Live**  C) Total Traffic Load  D) Terminal Transmission Log

**Q6**: curl -I command:
A) Installs package  B) Interactive mode  C) **Fetches headers only**  D) Ignores errors

**Q7**: Etag is used for:
A) Encryption  B) **Conditional requests/caching**  C) Session management  D) Compression

**Answers**: 1-A, 2-C, 3-B, 4-B, 5-B, 6-C, 7-B

---

## Short Answer (3 Questions)

**Q8**: What's the difference between A and CNAME records?

**Answer**: 
- A record: Maps domain to IPv4 address (e.g., example.com → 192.0.2.1)
- CNAME: Maps domain to another domain name (e.g., www.example.com → example.com)
- A records point to IPs; CNAMEs point to other domains

**Q9**: What is DNS spoofing and how to prevent it?

**Answer**:
- DNS spoofing: Attacker redirects domain to malicious IP address
- Detection: Cache poisoning, Man-in-the-Middle attacks
- Prevention:
  - Use DNSSEC (digital signatures)
  - Use trusted DNS resolvers (1.1.1.1, 8.8.8.8)
  - Monitor DNS answers for unexpected changes
  - Use HTTPS to detect certificate mismatches

**Q10**: Design a Content Delivery Network (CDN) architecture

**Answer**:
```
Global CDN Architecture:
1. Distribute edge servers globally
   - US: 10 datacenters
   - Europe: 8 datacenters
   - Asia: 6 datacenters

2. GeoDNS routing
   - User queries DNS
   - DNS returns closest edge IP
   - User gets content from nearby server

3. Caching strategy
   - Cache static assets (images, CSS, JS)
   - TTL: 1 year for versioned files
   - TTL: 1 hour for dynamic content

4. Cache invalidation
   - Versioning: app.v1.2.3.js
   - Tag-based purging
   - Time-based expiration

5. Monitoring
   - Edge performance metrics
   - Cache hit rate (target: >95%)
   - Origin offload
   - User experience metrics

6. Failover
   - Multiple origin servers
   - Health checks on origins
   - Fallback to alternate edge
```

---
