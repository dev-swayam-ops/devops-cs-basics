# 06 - Networking Fundamentals

## What You'll Learn

- OSI Model layers
- IP addressing (IPv4 and IPv6)
- Ports and sockets
- DNS basics
- Network interfaces
- Routing and default gateway
- Network diagnostics (ping, netstat, ss)

## Prerequisites

- Completed **05-storage-and-filesystems**
- Basic understanding of networks
- IP address familiarity

## Key Concepts

### 1. OSI Model (7 Layers)
```
7. Application (HTTP, SSH, DNS)
6. Presentation (encryption, encoding)
5. Session (connection management)
4. Transport (TCP, UDP)
3. Network (IP routing)
2. Data Link (MAC, Ethernet)
1. Physical (cables, signals)
```

### 2. IP Addressing
- **IPv4**: 4 octets (192.168.1.1), 32-bit
- **IPv6**: 6 blocks (2001:db8::1), 128-bit
- **Subnet Mask**: Divides network and host (255.255.255.0)
- **CIDR Notation**: /24 = last 8 bits host (256 addresses)

### 3. Ports (Transport)
- **Port 80**: HTTP
- **Port 443**: HTTPS
- **Port 22**: SSH
- **Port 53**: DNS
- **Ranges**: 0-1023 (well-known), 1024-65535 (ephemeral)

### 4. TCP vs UDP
- **TCP**: Reliable, ordered, connection (web, ssh)
- **UDP**: Fast, unreliable (DNS, video)
- **Port**: Protocol + port number

### 5. Network Interface
- **eth0, wlan0**: Network device names
- **MAC Address**: Physical layer (48-bit)
- **IP Address**: Network layer (logical)

## Hands-on Lab: Network Configuration

### Lab Overview
Explore network interfaces, routing, and connectivity.

### Lab Commands

```bash
# 1. List network interfaces
ip link show

# Expected:
# 1: lo: <LOOPBACK,UP>
# 2: eth0: <BROADCAST,RUNNING,MULTICAST>

# 2. Show IP addresses
ip addr show

# Expected:
# eth0: 192.168.1.100/24
# lo: 127.0.0.1/8

# 3. Show routing table
ip route show

# Expected:
# default via 192.168.1.1 dev eth0
# 192.168.1.0/24 dev eth0 proto kernel

# 4. Test connectivity
ping -c 4 8.8.8.8

# Expected: (4 responses, no loss)
# --- 8.8.8.8 statistics ---
# 4 packets transmitted, 4 received, 0% loss

# 5. Check which port is open
netstat -tuln | grep LISTEN

# Expected:
# tcp 0 0 0.0.0.0:22 0.0.0.0:* LISTEN
# tcp 0 0 127.0.0.1:5432 0.0.0.0:* LISTEN

# 6. Show DNS resolver
cat /etc/resolv.conf

# Expected:
# nameserver 8.8.8.8
# nameserver 8.8.4.4

# 7. Test DNS resolution
nslookup google.com

# Expected:
# Name: google.com
# Address: 142.251.32.14

# 8. Trace route to host
traceroute 8.8.8.8

# Expected: (hops to destination)
# 1 router (192.168.1.1)
# 2 isp-gateway
# ...

# 9. Show active connections
ss -tuln

# Expected: (similar to netstat)

# 10. Interface statistics
ifconfig

# Expected:
# eth0: inet 192.168.1.100
# RX packets: 5000, TX packets: 3000
```

## Validation

```bash
# Can you list interfaces?
ip link show && echo "✓ Interfaces shown"

# Know IP addresses?
ip addr show && echo "✓ IP addresses shown"

# Understand routing?
ip route show && echo "✓ Routing visible"

# Test connectivity?
ping -c 1 127.0.0.1 && echo "✓ Ping works"
```

## Cleanup

No cleanup needed (read-only commands).

## Common Mistakes

1. **Confusing IP and MAC**: IP is logical, MAC is physical
2. **Port confusion**: Ports are per protocol (TCP vs UDP)
3. **Subnet math**: /24 ≠ 24 bits (it's 256 addresses)
4. **Default gateway**: Must be in same subnet
5. **DNS vs IP**: DNS resolves names to IPs

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No internet | Check: ip route, ping gateway |
| Slow network | Check: packet loss (ping), latency (traceroute) |
| Port in use | Check: ss -tuln, kill process |
| DNS not resolving | Check: /etc/resolv.conf, nslookup |
| Interface down | Check: ip link show, ifup eth0 |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **07-dns-and-http-basics** after completion

## Additional Resources

- IP: `man 7 ip`
- Routing: `man route`, `man ip-route`
- Netstat: `man netstat`
- Network tools: `man ping`, `man traceroute`, `man nslookup`

