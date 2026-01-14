# 06 - Networking Fundamentals

## What You'll Learn

In this module, you'll master networking concepts essential for DevOps:
- OSI model and network layers
- TCP/IP stack and protocols
- Network configuration and diagnostics
- Routing and packet forwarding
- Common networking tools and troubleshooting
- Performance optimization

## Prerequisites

- Completed "05-storage-and-filesystems"
- Basic understanding of IP addresses and ports
- Linux command-line comfort

## Key Concepts

### 1. OSI Model (7 Layers)
- **Layer 7**: Application (HTTP, DNS, SSH)
- **Layer 6**: Presentation (Encryption, compression)
- **Layer 5**: Session (Connection management)
- **Layer 4**: Transport (TCP, UDP)
- **Layer 3**: Network (IP routing)
- **Layer 2**: Data Link (MAC, Ethernet)
- **Layer 1**: Physical (Cables, signals)

### 2. TCP vs UDP
- **TCP**: Connection-oriented, reliable, ordered delivery
- **UDP**: Connectionless, fast, no guarantees

### 3. Network Configuration
- IP addresses (IPv4, IPv6)
- Subnets and CIDR notation
- Default gateway and routing
- DNS resolution

### 4. Common Protocols
- **ICMP**: Diagnostics (ping, traceroute)
- **ARP**: IP to MAC mapping
- **DHCP**: Dynamic IP assignment
- **DNS**: Domain name resolution

## Hands-on Lab: Network Configuration and Diagnostics

### Lab Steps

```bash
# 1. Check IP configuration
ip addr show
ip -4 addr show

# 2. View routing table
ip route show
route -n

# 3. Test DNS resolution
nslookup google.com
dig google.com

# 4. Test connectivity
ping -c 4 8.8.8.8

# 5. Trace path to destination
traceroute google.com

# 6. Check listening ports
ss -tlnp
netstat -tulpn

# 7. Test specific port connectivity
telnet example.com 80
curl -v http://example.com

# 8. Check network interface statistics
ip -s link show
ifstat

# 9. Monitor real-time network activity
iftop -n
nethogs

# 10. DNS performance check
time dig google.com
```

## Validation

Verify your networking knowledge:

```bash
# Can you resolve DNS?
nslookup google.com && echo "✓ DNS working"

# Can you ping hosts?
ping -c 1 8.8.8.8 && echo "✓ Internet connectivity OK"

# Can you see routing?
ip route && echo "✓ Routes visible"

# Can you list ports?
ss -tlnp && echo "✓ Port listing OK"
```

## Cleanup

No system changes were made. No cleanup needed.

## Common Mistakes

1. **Forgetting CIDR notation**: `/24` means 254 usable hosts
2. **Confusing TCP and UDP**: TCP guarantees delivery, UDP is faster
3. **DNS resolution vs connectivity**: Ping tests connectivity, nslookup tests DNS
4. **Port vs IP**: Port belongs to specific IP, not standalone
5. **TTL confusion**: TTL = hops remaining, not wall-clock time

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No internet | Check: ping gateway, DNS, default route |
| Can't resolve DNS | Check: /etc/resolv.conf, systemd-resolved, ping IP directly |
| Slow network | Use: mtr, iftop, tcpdump to diagnose |
| Can't reach host | Use: traceroute to find where connection fails |
| Port unreachable | Check: port is listening, firewall rules, service running |

## Next Steps

1. Move to **07-dns-and-http-basics** for higher-layer protocols
2. Practice setting up network interfaces manually
3. Learn about firewalls and iptables
4. Study load balancing and network architecture
5. Explore advanced routing and VPNs

## Additional Resources

- Linux networking man pages: `man 7 networking`
- OSI model: https://en.wikipedia.org/wiki/OSI_model
- TCP/IP guide: https://tools.ietf.org/html/rfc791

