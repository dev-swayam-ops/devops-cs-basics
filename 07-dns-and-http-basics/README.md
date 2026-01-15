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
```
User asks: "What is google.com?"
    ↓
Recursive resolver (ISP)
    ↓
Root nameserver
    ↓
TLD nameserver (.com)
    ↓
Authoritative nameserver
    ↓
Returns: 142.251.32.14
```

### 2. DNS Record Types
- **A**: IPv4 address
- **AAAA**: IPv6 address
- **CNAME**: Alias to another domain
- **MX**: Mail exchange (email server)
- **TXT**: Text record (DKIM, SPF)
- **NS**: Nameserver delegation

### 3. HTTP Request/Response
```
Request:
GET /api/users HTTP/1.1
Host: example.com
Accept: application/json

Response:
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 42

{"users": [{"id": 1, "name": "Alice"}]}
```

### 4. HTTP Status Codes
- **2xx**: Success (200 OK, 201 Created)
- **3xx**: Redirect (301 Moved, 304 Not Modified)
- **4xx**: Client error (400 Bad, 404 Not Found)
- **5xx**: Server error (500 Internal, 503 Service Unavailable)

### 5. HTTPS/TLS
- **Port 443**: Encrypted HTTP
- **Certificate**: Proves domain ownership
- **Encryption**: HTTPS = HTTP + TLS

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

