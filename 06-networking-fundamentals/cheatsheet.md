# 06 - Networking Fundamentals Cheatsheet

## Interface Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ip link show` | List interfaces | eth0, wlan0, lo |
| `ip addr show` | IP addresses | 192.168.1.100/24 |
| `ip -s link show` | Interface stats | RX/TX packets, errors |
| `ifconfig` | Legacy interface info | Older tool, same info |
| `ethtool eth0` | Interface details | Speed, duplex, settings |

## Routing Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ip route show` | Routing table | Gateway, destinations |
| `ip route add` | Add route | Manual routing |
| `ip route del` | Delete route | Remove route |
| `traceroute host` | Path to destination | Show all hops |
| `tracepath host` | Path (no ICMP) | Alternative to traceroute |

## Connection Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ss -tuln` | Listening sockets | Open ports |
| `ss -tan` | All TCP sockets | All connections |
| `ss -uan` | All UDP sockets | UDP connections |
| `netstat -tuln` | Listening (legacy) | Old tool |
| `lsof -i :22` | Processes on port | What's using port 22 |

## Diagnostics Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ping -c N host` | Test connectivity | Response time |
| `traceroute host` | Route to destination | Hops and latency |
| `mtr host` | Continuous trace | Real-time path |
| `telnet host port` | Test TCP port | Verify port open |
| `nc -zv host port` | Port scan | Quick port check |

## DNS Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `nslookup domain` | DNS resolution | IP for domain |
| `dig domain` | Detailed DNS query | All record types |
| `getent hosts domain` | System resolver | Uses /etc/hosts first |
| `cat /etc/resolv.conf` | DNS servers | Configured resolvers |
| `systemctl restart systemd-resolved` | Restart DNS | Flush cache |

## IP Address Operations

| Concept | CIDR | Hosts | Example |
|---------|------|-------|---------|
| /32 | Single IP | 1 | 192.168.1.1/32 |
| /24 | Subnet | 254 | 192.168.1.0/24 |
| /16 | Network | 65534 | 10.0.0.0/16 |
| /8 | Large network | 16M | 10.0.0.0/8 |

## Common Ports

| Port | Protocol | Service |
|------|----------|---------|
| 22 | TCP | SSH |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 53 | UDP/TCP | DNS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 6379 | TCP | Redis |
| 8080 | TCP | Alternate HTTP |

## Network Configuration

| File | Purpose | Example |
|------|---------|---------|
| `/etc/hostname` | Hostname | myhost |
| `/etc/resolv.conf` | DNS servers | nameserver 8.8.8.8 |
| `/etc/hosts` | Local resolution | 127.0.0.1 localhost |
| `/etc/network/interfaces` | Interface config | Static/DHCP setup |

## Socket States

| State | Meaning | Notes |
|-------|---------|-------|
| LISTEN | Waiting for connection | Server port |
| ESTAB | Connected | Active connection |
| TIME_WAIT | Closing | Will close soon |
| SYN_SENT | Initiating | Connecting |
| CLOSE_WAIT | Peer closed | Waiting close |

## Network Tools Comparison

| Tool | What it shows | Modern |
|------|---------------|--------|
| netstat | Sockets, stats | Old (use ss) |
| ifconfig | Interfaces | Old (use ip) |
| route | Routing | Old (use ip route) |
| ss | Sockets (fast) | New ✓ |
| ip | Everything | New ✓ |

## OSI Layer Reference

| Layer | Name | Examples |
|-------|------|----------|
| 7 | Application | HTTP, SSH, DNS |
| 6 | Presentation | Encryption |
| 5 | Session | Connections |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP, routing |
| 2 | Data Link | MAC, Ethernet |
| 1 | Physical | Cables |

## TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliable | Yes | No |
| Ordered | Yes | No |
| Connection | Yes | No |
| Speed | Slower | Faster |
| Use | Web, SSH, FTP | DNS, Video, Games |

## Quick Diagnostics

```bash
# Interface status
ip link show; ip addr show

# Routing
ip route show

# Listening ports
ss -tuln

# Connectivity
ping -c 1 8.8.8.8

# DNS
nslookup google.com

# Full path
traceroute 8.8.8.8

# Active connections
ss -tan
```

## Common IPv4 Address Ranges

| Range | Class | Private |
|-------|-------|---------|
| 10.0.0.0/8 | A | Yes |
| 172.16.0.0/12 | B | Yes |
| 192.168.0.0/16 | C | Yes |
| 127.0.0.0/8 | Loopback | N/A |
| 169.254.0.0/16 | Link-local | N/A |
