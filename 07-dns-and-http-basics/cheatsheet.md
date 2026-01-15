# 07 - DNS and HTTP Basics Cheatsheet

## DNS Query Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `nslookup domain` | Lookup IP address | nslookup google.com |
| `dig domain` | Detailed DNS query | dig google.com |
| `dig domain A` | Query A record | IPv4 address |
| `dig domain AAAA` | Query AAAA record | IPv6 address |
| `dig domain MX` | Mail servers | dig google.com MX |
| `dig domain NS` | Nameservers | Authoritative servers |
| `dig domain TXT` | Text records | SPF, DKIM records |
| `dig domain +short` | Short output | Just the answer |
| `host domain` | Simple lookup | Alternative to nslookup |
| `getent hosts domain` | System resolver | Uses /etc/hosts first |

## DNS Record Types

| Type | Purpose | Example |
|------|---------|---------|
| A | IPv4 address | 192.168.1.1 |
| AAAA | IPv6 address | 2001:db8::1 |
| CNAME | Alias | www → example.com |
| MX | Mail server | mail.example.com |
| NS | Nameserver | Delegation |
| TXT | Text record | SPF, DKIM, verification |
| SOA | Start of Authority | Primary nameserver |
| PTR | Reverse DNS | IP to hostname |

## HTTP Request Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `curl URL` | Fetch page | curl https://example.com |
| `curl -I URL` | Headers only | curl -I https://example.com |
| `curl -X GET` | GET request | Default method |
| `curl -X POST` | POST request | curl -X POST -d "data" |
| `curl -X PUT` | PUT request | Update resource |
| `curl -X DELETE` | DELETE request | Delete resource |
| `curl -H "Header: value"` | Custom header | curl -H "Accept: json" |
| `curl -d "data"` | Send data | POST body |
| `curl -L` | Follow redirects | curl -L http://example.com |
| `curl -u user:pass` | Basic auth | curl -u admin:secret |

## HTTP Status Codes

| Code Range | Meaning | Examples |
|-----------|---------|----------|
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirect | 301 Moved, 302 Found, 304 Not Modified |
| 4xx | Client Error | 400 Bad Request, 404 Not Found, 403 Forbidden |
| 5xx | Server Error | 500 Internal, 503 Service Unavailable |

## HTTP Methods

| Method | Purpose | Safe | Idempotent |
|--------|---------|------|-----------|
| GET | Fetch data | Yes | Yes |
| POST | Create/Submit | No | No |
| PUT | Update entire | No | Yes |
| DELETE | Remove | No | Yes |
| PATCH | Partial update | No | No |
| HEAD | Like GET, no body | Yes | Yes |

## HTTPS and Certificates

| Command | Purpose | Example |
|---------|---------|---------|
| `openssl s_client -connect host:443` | Check certificate | Full details |
| `openssl s_client -connect host:443 -brief` | Certificate brief | Summary only |
| `openssl x509 -in cert.pem -text` | Read certificate | Display contents |
| `openssl req -new -key key.pem -out req.csr` | Create CSR | Certificate request |

## HTTP Headers (Common)

| Header | Purpose | Example |
|--------|---------|---------|
| Content-Type | Response format | application/json |
| Content-Length | Body size | 1256 |
| Authorization | Auth credentials | Bearer token |
| Accept | Requested format | application/json |
| User-Agent | Client info | curl/7.64.1 |
| Host | Server hostname | example.com |
| Cache-Control | Caching policy | max-age=3600 |
| Set-Cookie | Session cookie | session_id=abc123 |

## DNS Resolution Process

```
1. Browser asks resolver (ISP)
2. Resolver asks root nameserver
3. Root points to TLD (.com)
4. TLD points to authoritative
5. Authoritative returns IP
6. Resolver caches and returns
7. Browser connects to IP
```

## SSL/TLS Certificate Chain

| Level | Purpose | Example |
|-------|---------|---------|
| Root CA | Trust anchor | DigiCert Global Root |
| Intermediate CA | Bridge | DigiCert SHA2 |
| End Entity | Domain | google.com |

## DNS Servers (Public)

| Provider | Primary | Secondary |
|----------|---------|-----------|
| Google | 8.8.8.8 | 8.8.4.4 |
| Cloudflare | 1.1.1.1 | 1.0.0.1 |
| OpenDNS | 208.67.222.222 | 208.67.220.220 |
| Quad9 | 9.9.9.9 | 149.112.112.112 |

## HTTP Protocol Versions

| Version | Features | Status |
|---------|----------|--------|
| HTTP/1.0 | Basic | Old |
| HTTP/1.1 | Keep-alive, Pipelining | Common |
| HTTP/2 | Multiplexing, Compression | Modern |
| HTTP/3 | QUIC protocol | Latest |

## Common Ports

| Port | Protocol | Service |
|------|----------|---------|
| 53 | UDP/TCP | DNS |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 8080 | TCP | HTTP Alternate |
| 8443 | TCP | HTTPS Alternate |

## curl Common Options

```bash
# Headers and body
curl -i https://example.com      # Include headers

# Verbose output
curl -v https://example.com      # Debug info

# Follow redirects
curl -L https://example.com      # Follow 3xx

# Authentication
curl -u user:pass https://       # Basic auth

# Custom header
curl -H "X-Token: abc" https://  # Add header

# Send JSON
curl -d '{"key":"val"}' \
  -H "Content-Type: application/json" https://
```

## Quick DNS Troubleshooting

```bash
# Check resolution
nslookup example.com

# Detailed query
dig example.com

# All records
dig example.com ANY

# Specific server
dig @8.8.8.8 example.com

# Reverse DNS
dig -x 93.184.216.34
```
