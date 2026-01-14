# 06 - Networking Fundamentals: Quiz

## MCQ (7 Questions)

**Q1**: What layer does TCP operate on?
A) Physical  B) Network  C) **Transport** D) Application

**Q2**: In TCP handshake, what is the first packet called?
A) ACK  B) **SYN** C) FIN D) PSH

**Q3**: What is the purpose of DNS?
A) Routing packets  B) Encrypting traffic  C) **Translating domains to IPs** D) Managing bandwidth

**Q4**: What does ICMP do?
A) Transfers files  B) **Network diagnostics** C) Encrypts connections D) Manages routing

**Q5**: What command shows listening ports?
A) ping  B) traceroute  C) **ss -tlnp** D) dig

**Q6**: What is a socket?
A) Network cable  B) **IP:port endpoint** C) Switch port D) Fiber connection

**Q7**: What happens in FIN_WAIT state?
A) Connection starting  B) **Waiting for ACK after close** C) Listening for connections D) Transferring data

**Answers**: 1-C, 2-B, 3-C, 4-B, 5-C, 6-B, 7-B

---

## Short Answer (3 Questions)

**Q8**: Explain TCP 3-way handshake
**A**: Client sends SYN, server responds SYN-ACK, client sends ACK. Establishes connection.

**Q9**: How to test DNS resolution?
**A**: `nslookup domain` or `dig domain` to check if domain resolves to IP. Check `/etc/resolv.conf` for DNS servers.

**Q10**: Design network monitoring
**A**: Monitor latency, packet loss, bandwidth. Alert on packet loss >1%, latency >100ms. Use mtr, iftop for diagnostics.

---
