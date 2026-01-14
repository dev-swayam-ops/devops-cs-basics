# 06 - Networking Fundamentals: Cheatsheet

## Network Configuration Commands

```bash
# IP Configuration
ip addr show              # Show all IPs
ip addr add 192.168.1.100/24 dev eth0
ip addr del 192.168.1.100/24 dev eth0

# Routing
ip route show             # Show routes
ip route add default via 192.168.1.1
ip route del 192.168.1.0/24

# DNS
cat /etc/resolv.conf      # DNS settings
systemctl restart systemd-resolved

# Bring interface up/down
ip link set eth0 up
ip link set eth0 down

# Check MTU
ip link show eth0
ip link set eth0 mtu 1500
```

## Diagnostic Commands

```bash
ping -c 4 host                    # ICMP test
mtr host                          # Traceroute + latency
traceroute host                   # Path to destination
netstat -an                       # All connections
ss -tlnp                          # Listening ports
nslookup domain                   # DNS lookup
dig domain +trace                 # Full DNS trace
curl -v http://example.com        # HTTP debug
curl -I http://example.com        # HTTP headers only
```

## TCP/IP Stack

```
Application Layer: HTTP, HTTPS, DNS, SSH, SMTP
Transport Layer: TCP, UDP
Network Layer: IP, ICMP, ARP
Link Layer: Ethernet, PPP
Physical Layer: Cables, Hubs, Repeaters
```

## TCP Connection States

```
LISTEN → SYN_SENT → SYN_RECEIVED → ESTABLISHED → FIN_WAIT → CLOSED

ESTABLISHED: Active connection
TIME_WAIT: Waiting after close
CLOSE_WAIT: Waiting for app to close
FIN_WAIT: Waiting for ACK
```

## Network Troubleshooting Checklist

```
1. Is host reachable? (ping)
2. Is route correct? (traceroute)
3. Is port open? (telnet, ss)
4. Is service running? (netstat, lsof)
5. Is firewall blocking? (iptables -L)
6. Is DNS resolving? (nslookup, dig)
7. Is bandwidth limited? (iftop, nethogs)
8. Are packets dropping? (mtr)
```

---
