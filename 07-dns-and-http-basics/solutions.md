# 07 - DNS and HTTP: Solutions, Cheatsheet & Quiz (Continued)

## Solutions Summary

### DNS Record Types
- **A**: IPv4 address (193.109.239.14)
- **AAAA**: IPv6 address
- **CNAME**: Alias to another domain
- **MX**: Mail server
- **NS**: Nameserver
- **TXT**: Text records (SPF, DKIM)
- **SOA**: Start of Authority (zone info)

### HTTP Status Codes
- 1xx: Informational
- 2xx: Success (200 OK, 201 Created)
- 3xx: Redirect (301 Moved, 304 Not Modified)
- 4xx: Client Error (400, 403 Forbidden, 404 Not Found)
- 5xx: Server Error (500, 503 Service Unavailable)

### Caching Headers
```
Cache-Control: max-age=3600        # Cache 1 hour
Cache-Control: no-cache            # Validate with server
Cache-Control: no-store            # Don't cache at all
Cache-Control: public              # Cache in public proxies
Cache-Control: private             # Cache only locally
Expires: Wed, 21 Oct 2024 07:28:00 GMT
Etag: "123456789"                  # Validate resource
Last-Modified: Wed, 21 Oct 2024 07:28:00 GMT
```

## Cheatsheet

| Command | Purpose |
|---------|---------|
| `dig domain` | Full DNS query with details |
| `dig +short domain` | Just the answer |
| `dig +trace domain` | Show full path |
| `dig -x IP` | Reverse DNS lookup |
| `nslookup domain` | Query specific record |
| `curl -I url` | Headers only |
| `curl -vI url` | Verbose headers |
| `curl -L url` | Follow redirects |
| `curl -H "Header: value"` | Add custom header |

## Quiz - Full Answers

**Q1-Q7**: A) Domain Name System, B) Application, B) Moved Permanently, B) Encryption, B) Time To Live, C) Fetches headers only, B) Conditional requests

**Q8**: A records = IP address, CNAME = alias to another domain

**Q9**: DNS spoofing = attacker redirects domain to malicious IP. Prevented by DNSSEC and secure resolvers.

**Q10**: CDN design:
- Distribute edge servers globally
- Use GeoDNS to route users to nearest server
- Cache content at edges
- Implement cache invalidation
- Monitor performance/availability

---
