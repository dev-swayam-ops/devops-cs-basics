# 07 - DNS and HTTP Basics

## What You'll Learn

Master DNS and HTTP concepts:
- DNS resolution process and hierarchy
- A, AAAA, CNAME, MX, TXT records
- DNS caching and performance
- HTTP protocol versions (1.0, 1.1, 2.0, 3.0)
- Request/response cycle
- Status codes and headers
- HTTPS and certificate basics

## Prerequisites

- Completed **06-networking-fundamentals**
- Understanding of IP addresses and domains
- Basic HTTP usage

## Key Concepts

### 1. DNS Hierarchy
```
Root (.)
  |
TLD (.com, .org, .net)
  |
Domain (google.com)
  |
Subdomain (mail.google.com)
```

### 2. DNS Records
- **A Record**: IPv4 address (example.com -> 93.184.216.34)
- **AAAA Record**: IPv6 address
- **CNAME Record**: Alias to another domain
- **MX Record**: Mail server
- **TXT Record**: Text metadata, SPF, DKIM
- **NS Record**: Nameserver

### 3. HTTP Methods
- **GET**: Retrieve resource
- **POST**: Submit data
- **PUT**: Replace resource
- **DELETE**: Remove resource
- **PATCH**: Partial update
- **HEAD**: Like GET but no body

### 4. Status Codes
- **2xx**: Success (200 OK, 201 Created)
- **3xx**: Redirect (301 Moved, 304 Not Modified)
- **4xx**: Client error (404 Not Found, 401 Unauthorized)
- **5xx**: Server error (500 Internal, 503 Service Unavailable)

## Hands-on Lab: DNS and HTTP

### Lab Steps

```bash
# 1. DNS lookup
nslookup google.com
dig google.com
dig google.com +short

# 2. Query specific DNS record
dig google.com A
dig google.com AAAA
dig google.com MX
dig google.com TXT

# 3. Reverse DNS lookup
nslookup 8.8.8.8
dig -x 8.8.8.8

# 4. Trace DNS resolution path
dig google.com +trace

# 5. Test DNS performance
time dig google.com

# 6. Check local DNS cache
systemctl status systemd-resolved
resolvectl query google.com

# 7. Make HTTP request
curl http://example.com
curl -v http://example.com

# 8. Check HTTP headers
curl -i http://example.com
curl -I http://example.com

# 9. POST request
curl -X POST -d "data=value" http://httpbin.org/post

# 10. Follow redirects
curl -L http://example.com
```

## Validation

Verify your DNS/HTTP knowledge:

```bash
# Can you resolve DNS?
dig google.com +short && echo "✓ DNS resolution works"

# Can you query record types?
dig google.com MX && echo "✓ DNS records visible"

# Can you check HTTP?
curl -I http://example.com && echo "✓ HTTP request works"

# Can you understand status codes?
echo "✓ Status code concepts understood"
```

## Cleanup

```bash
# No cleanup needed
echo "✓ Lab complete"
```

## Common Mistakes

1. **DNS caching**: Local DNS cache can hide changes
2. **CNAME at root**: Can't have CNAME at domain root
3. **HTTP vs HTTPS**: Not the same, HTTPS is encrypted
4. **Status codes**: 3xx means redirect, not success
5. **DNS TTL**: Lower TTL for flexibility, higher for performance

## Troubleshooting

| Problem | Solution |
|---------|----------|
| DNS not resolving | Check: /etc/resolv.conf, DNS server alive, flush cache |
| Slow DNS | Use: public DNS (8.8.8.8), check TTL, reduce lookups |
| HTTP 404 | Check: URL correct, page exists, check logs |
| Certificate error | Check: HTTPS enabled, cert valid, CA trusted |
| DNS propagation | Wait: DNS changes take time (TTL), clear cache |

## Next Steps

1. Move to **08-linux-kernel-basics** for kernel networking
2. Learn about Load Balancers and reverse proxies
3. Study REST API design
4. Explore CDN and caching strategies
5. Learn about DNSSEC

## Additional Resources

- DNS: `man dig`, `man nslookup`
- HTTP: https://tools.ietf.org/html/rfc7231
- HTTP status codes: https://httpwg.org/
- HTTPS: https://www.ssl.com/article/

