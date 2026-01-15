# 07 - DNS and HTTP Basics Solutions

## Easy Solutions (1-5)

### Solution 1: Simple DNS Lookup

**Command**:
```bash
nslookup google.com
```

**Output Example**:
```
Server: 8.8.8.8
Address: 8.8.8.8#53

Name: google.com
Address: 142.251.32.14
Address: 2607:f8b0:4004:807::200e
```

**Answers**:
- IP: 142.251.32.14 (IPv4)
- Also: 2607:f8b0:4004:807::200e (IPv6)
- Results: consistent (DNS cached)

---

### Solution 2: Query A Record

**Command**:
```bash
dig google.com A
```

**Output Example**:
```
;; ANSWER SECTION:
google.com.     300 IN  A   142.251.32.14

;; Query time: 50 msec
```

**Analysis**:
- Record type: A (IPv4)
- TTL: 300 seconds (5 minutes)
- Domain: google.com
- IP: 142.251.32.14

---

### Solution 3: Query MX Records

**Command**:
```bash
dig google.com MX
```

**Output Example**:
```
;; ANSWER SECTION:
google.com.  3600 IN  MX  10 aspmx.l.google.com.
google.com.  3600 IN  MX  20 alt1.aspmx.l.google.com.
google.com.  3600 IN  MX  30 alt2.aspmx.l.google.com.
```

**Analysis**:
- MX records: 3 mail servers
- Priority: 10, 20, 30 (lower = preferred)
- Multiple: redundancy/load balancing

---

### Solution 4: Test HTTP Request

**Command**:
```bash
curl -I https://example.com
```

**Output Example**:
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1256
Server: ECS (dcb/7F85)
Cache-Control: max-age=604800
```

**Analysis**:
- Status: 200 OK (success)
- Type: text/html (web page)
- Length: 1256 bytes
- `-I` flag: headers only (no body)

---

### Solution 5: Check Certificate

**Command**:
```bash
openssl s_client -connect google.com:443 -brief
```

**Output Example**:
```
verify OK
depth=2 C = US, O = Google Trust Services LLC
depth=1 CN = Google Internet Authority G3
depth=0 CN = google.com

Certificate chain: OK
subject=CN = google.com
issuer=C = US, O = Google
```

**Analysis**:
- Valid: yes
- Issuer: Google Internet Authority
- Subject: google.com
- Proves: domain ownership

---

## Medium Solutions (6-10)

### Solution 6: Test HTTP Methods

**Commands**:
```bash
curl https://httpbin.org/get
curl -X POST -d "data=test" https://httpbin.org/post
```

**Output Example**:
```
GET response:
{
  "url": "https://httpbin.org/get",
  "args": {}
}

POST response:
{
  "form": {
    "data": "test"
  },
  "url": "https://httpbin.org/post"
}
```

**Differences**:
- GET: reads data
- POST: sends data
- POST modifies (or should)

---

### Solution 7: Follow Redirects

**Commands**:
```bash
curl -I http://example.com
curl -I -L http://example.com
```

**Output Example**:
```
First response:
HTTP/1.1 301 Moved Permanently
Location: https://example.com/

After -L flag:
HTTP/2 200
(final page loads)
```

**Analysis**:
- Status: 301 (permanent redirect)
- Location: https (encrypted)
- `-L` follows all redirects

---

### Solution 8: Analyze HTTP Headers

**Command**:
```bash
curl -I -H "User-Agent: MyBot" https://example.com
```

**Output Example**:
```
HTTP/2 200
Content-Type: text/html
Cache-Control: max-age=604800
Server: ECS
Date: Wed, 15 Jan 2026 10:00:00 GMT
```

**Analysis**:
- Cache-Control: max-age=604800 (7 days)
- Server: ECS (infrastructure)
- Custom headers: usually accepted

---

### Solution 9: Query Record Types

**Commands**:
```bash
dig example.com A      # IPv4
dig example.com AAAA   # IPv6
dig example.com NS     # Nameservers
dig example.com TXT    # Text records
```

**Example Outputs**:
```
A:    93.184.216.34
AAAA: 2606:2800:220:1:248:1893:25c8:1946
NS:   a.iana-servers.net, b.iana-servers.net
TXT:  "v=spf1 -all"
```

---

### Solution 10: Compare DNS Servers

**Commands**:
```bash
nslookup google.com 8.8.8.8        # Google
nslookup google.com 1.1.1.1        # Cloudflare
nslookup google.com 208.67.222.222 # OpenDNS
```

**Output Example**:
```
All return: 142.251.32.14
(Same result, different servers)
```

**Analysis**:
- Results: consistent across servers
- Fastest: typically Cloudflare (1.1.1.1)
- Local resolver: usually slower

---

## Quick Reference

```bash
# DNS lookup
nslookup domain.com
dig domain.com A

# HTTP request
curl -I https://example.com        # headers
curl -X POST -d "data" https://    # POST

# Certificate check
openssl s_client -connect domain.com:443

# Follow redirects
curl -L http://example.com

# Send headers
curl -H "Custom-Header: value" https://
```
