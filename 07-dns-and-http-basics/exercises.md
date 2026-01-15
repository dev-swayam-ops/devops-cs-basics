# 07 - DNS and HTTP Basics Exercises

## Easy Exercises (1-5)

### Exercise 1: Simple DNS Lookup
**Objective**: Resolve domain to IP.

**Task**:
- Run: `nslookup google.com`
- What's the IP address?
- Is it IPv4 or IPv6?
- Run same command but with different domain
- Results consistent across lookups?

---

### Exercise 2: Query A Record
**Objective**: Understand A record type.

**Task**:
- Run: `dig google.com A`
- What record type shown? (A, AAAA?)
- TTL value? (300, 3600?)
- Can you identify IP, domain, TTL?
- What does TTL mean? (time to live)

---

### Exercise 3: Query MX Records
**Objective**: Find mail servers.

**Task**:
- Run: `dig google.com MX`
- What's MX record?
- How many mail servers?
- What's priority number?
- Why multiple MX records?

---

### Exercise 4: Test HTTP Request
**Objective**: Fetch web page.

**Task**:
- Run: `curl -I https://example.com`
- What's status code?
- What's Content-Type?
- What's Content-Length?
- What does -I mean? (headers only)

---

### Exercise 5: Check Certificate
**Objective**: View SSL/TLS certificate.

**Task**:
- Run: `openssl s_client -connect google.com:443 -brief`
- Is certificate valid?
- What's issuer? (who signed it)
- Expiry date?
- What does certificate prove?

---

## Medium Exercises (6-10)

### Exercise 6: Test Different HTTP Methods
**Objective**: Understand GET vs POST.

**Task**:
- GET: `curl https://httpbin.org/get`
- POST: `curl -X POST -d "data=test" https://httpbin.org/post`
- What's difference in response?
- GET = read, POST = write?
- Which modifies server?

---

### Exercise 7: Follow Redirects
**Objective**: See HTTP redirects (3xx).

**Task**:
- Run: `curl -I http://example.com` (note: http, not https)
- Does it redirect?
- Status code? (301, 302, 307?)
- Where redirects to?
- Follow: `curl -L http://example.com` (follow redirects)

---

### Exercise 8: Analyze HTTP Headers
**Objective**: Understand request/response headers.

**Task**:
- Run: `curl -I -H "User-Agent: MyBot" https://example.com`
- What headers returned?
- Cache-Control value?
- Server header? (what server running?)
- Add custom header, does server receive?

---

### Exercise 9: Query Different DNS Records
**Objective**: Explore all record types.

**Task**:
- A record: `dig example.com A`
- AAAA record: `dig example.com AAAA`
- NS record: `dig example.com NS`
- TXT record: `dig example.com TXT`
- Which records exist for your domain?

---

### Exercise 10: Compare DNS Servers
**Objective**: Test different resolvers.

**Task**:
- Google DNS: `nslookup google.com 8.8.8.8`
- Cloudflare: `nslookup google.com 1.1.1.1`
- Local: `nslookup google.com 127.0.0.1` (may fail)
- Compare results (same IP?)
- Which is fastest?

---

## Submission Tips

1. Use `dig` for detailed DNS (better than nslookup)
2. Use `curl -I` to see headers without body
3. Use `curl -L` to follow redirects
4. Remember: status code 200 = success
5. HTTPS needs valid certificate (https://)
