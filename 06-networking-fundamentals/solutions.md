# 06 - Networking Fundamentals: Solutions, Cheatsheet & Quiz

## Solutions Summary

### OSI Layers
- Layer 7: Application (HTTP, DNS, SSH)
- Layer 6: Presentation (Encryption, compression)
- Layer 5: Session (Connection management)
- Layer 4: Transport (TCP, UDP)
- Layer 3: Network (IP routing)
- Layer 2: Data Link (MAC, Ethernet)
- Layer 1: Physical (Cables, signals)

### Common Network Commands

| Command | Purpose |
|---------|---------|
| `ip addr show` | View IP addresses |
| `ip route show` | View routing table |
| `ss -tlnp` | List listening sockets |
| `nslookup domain` | DNS lookup |
| `ping -c 4 host` | ICMP connectivity test |
| `mtr host` | Traceroute + latency |
| `netstat -tulpn` | Show all services |
| `tcpdump -i eth0` | Capture packets |
| `iftop` | Real-time bandwidth usage |

## Quick Quiz

**Q1**: What layer does TCP operate on?
**A**: Layer 4 (Transport)

**Q2**: What happens in TCP 3-way handshake?
**A**: SYN → SYN-ACK → ACK

**Q3**: What is DNS A record?
**A**: Maps domain name to IPv4 address

**Q4**: How to block port 443 with iptables?
**A**: `iptables -I INPUT -p tcp --dport 443 -j DROP`

**Q5**: What is the purpose of ICMP?
**A**: Network diagnostics, error reporting (ping, traceroute)

**Q6**: What is a socket?
**A**: Endpoint of network connection (IP:port)

**Q7**: How many hops in a typical internet path?
**A**: 10-20 hops (varies by destination)

---

**Short Answer:**

**Q8**: Explain the TCP 3-way handshake
**A**: 
1. Client sends SYN (synchronization)
2. Server responds with SYN-ACK
3. Client sends ACK
Result: Connection established, can send data

**Q9**: You can't reach example.com but ping succeeds. Diagnose:
**A**:
```bash
# DNS issue:
nslookup example.com         # Failed?
dig example.com              # Check response

# Gateway issue:
ip route                     # Check default gateway
traceroute example.com       # Where does it fail?

# Firewall issue:
sudo iptables -L -n          # Check rules
ss -tlnp | grep 80          # Is port listening?
```

**Q10**: Design reliable network monitoring
**A**:
- Monitor: latency, packet loss, bandwidth
- Alerts: packet loss >1%, latency >100ms
- Tools: mtr, iftop, tcpdump
- Baseline: establish normal performance
- Correlation: link with application issues

---
