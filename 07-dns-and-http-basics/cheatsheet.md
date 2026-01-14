# 07 - DNS and HTTP: Cheatsheet & Quiz

## DNS Cheatsheet

```bash
# DNS Queries
nslookup example.com                    # Basic query
dig example.com                         # Full details
dig example.com +short                  # Answer only
dig example.com +trace                  # Full path
dig -x 192.168.1.1                     # Reverse lookup
dig example.com @8.8.8.8               # Query specific server
dig example.com MX                     # Mail servers
dig example.com NS                     # Nameservers

# Zone transfers
dig @ns1.example.com example.com AXFR  # Get full zone

# DNSSEC
dig example.com +dnssec                # Check DNSSEC
dig example.com +validate              # Validate signatures
```

## HTTP Cheatsheet

```bash
# HTTP Methods
curl -X GET http://example.com
curl -X POST -d "key=value" http://example.com
curl -X PUT -d "key=value" http://example.com
curl -X DELETE http://example.com
curl -X HEAD http://example.com        # Headers only, no body

# Headers and debugging
curl -I http://example.com             # Headers only
curl -v http://example.com             # Verbose output
curl -H "Custom-Header: value" url     # Add header
curl -H "Authorization: Bearer token" url
curl -b cookie.txt url                 # Use cookies
curl -c cookie.txt url                 # Save cookies

# Redirects and caching
curl -L http://example.com             # Follow redirects
curl -H "Cache-Control: no-cache" url
curl -H "If-None-Match: etag" url      # Conditional request
```

## HTTP Status Codes Reference

```
2xx Success:
- 200 OK
- 201 Created
- 202 Accepted
- 204 No Content

3xx Redirect:
- 301 Moved Permanently
- 302 Found (Temporary)
- 304 Not Modified
- 307 Temporary Redirect

4xx Client Error:
- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 429 Too Many Requests

5xx Server Error:
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout
```

---

## Quick Quiz

**1-7 Answers**: System, Application, Moved Permanently, Encryption, Time-To-Live, Headers only, Caching

**Q8**: A = IPv4, CNAME = domain alias

**Q9**: Malicious IP redirection; prevented by DNSSEC/secure resolvers

**Q10**: GeoDNS routing, edge caches, cache invalidation, failover

---
