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

The OSI (Open Systems Interconnection) model is a conceptual framework that describes how network communication happens. It divides networking into 7 layers, each with specific responsibilities.

**Layer 7: Application Layer**
- Where user applications and services operate (HTTP, SSH, DNS, SMTP, FTP)
- Data is in human-readable format (text, video, images, etc.)
- Examples: Web browser requests, email clients, remote login
- Developers typically work at this layer
- Handles user interaction, file transfer, remote access
- Protocol examples: HTTP/HTTPS, SSH, DNS, POP3, SMTP, Telnet

**Layer 6: Presentation Layer**
- Data translation, encryption, and compression
- Ensures data is in format that receiving application understands
- Converts between different character encodings (ASCII, Unicode)
- Handles encryption (SSL/TLS starts here) and decryption
- Compresses data to reduce bandwidth
- Example: Browser receives compressed HTML from web server, decompresses it

**Layer 5: Session Layer**
- Manages conversation between applications
- Establishes, maintains, and terminates connections
- Handles synchronization (recovery if connection drops mid-transfer)
- Keeps track of which packets belong to which session
- Allows multiple simultaneous conversations
- Example: When you upload a large file, this layer ensures the connection doesn't break

**Layer 4: Transport Layer**
- Decides how data flows end-to-end between applications
- Two main protocols: TCP (reliable) and UDP (fast)
- Adds port numbers to identify applications
- TCP ensures all data arrives in order, UDP just sends packets
- Multiplexing: multiple applications can use network simultaneously
- This is where you think about reliability vs speed

**Layer 3: Network Layer**
- Routing packets from source to destination across networks
- Uses IP addresses to identify computers (logical addressing)
- Router's primary responsibility layer
- Determines path packets take across internet
- Handles different networks being interconnected
- Example: Your packet goes through multiple routers to reach destination

**Layer 2: Data Link Layer**
- Communication between devices on same local network (ethernet, Wi-Fi)
- Uses MAC addresses (physical addresses) instead of IP
- Creates frames from packets, adds error checking
- Switches operate at this layer
- Ensures data transmitted correctly over cables/wireless
- Example: Your computer finds another computer on local network using MAC address

**Layer 1: Physical Layer**
- Actual wires, cables, radio signals, voltages
- Converting data to electrical/optical signals
- Network cables (Ethernet), fiber, wireless signals
- Hubs and repeaters operate here
- No concept of addresses or protocols - just raw signals
- Example: RJ45 cable plugged into network port

**Why Layers Matter**
- Separation of concerns: each layer handles one responsibility
- Troubleshooting: know which layer your problem is at
- Protocol independence: layers can change without affecting others
- Complexity management: layer abstraction makes networking understandable

### 2. IP Addressing

Every device on the internet needs a unique address so packets can be routed to the correct destination. IP addressing is the fundamental identification system.

**IPv4 (Internet Protocol Version 4)**
- 32 bits divided into 4 octets (bytes), written in decimal: `192.168.1.100`
- Each octet ranges 0-255
- Total possible addresses: 2^32 = 4.3 billion (why we ran out)
- Address structure: Network part + Host part
- Example: In `192.168.1.100`, first 3 octets are network, last is host
- Used on almost every device today
- Format: `XXX.XXX.XXX.XXX` where each X is 0-255

**IPv6 (Internet Protocol Version 6)**
- 128 bits (4x larger than IPv4), written in hexadecimal: `2001:0db8:0000:0000:0000:ff00:0042:8329`
- Compressed notation: `2001:db8::ff00:42:8329` (consecutive zeros become ::)
- Total possible addresses: 2^128 (essentially unlimited)
- Designed to replace IPv4 as world runs out of addresses
- Still not widely deployed - dual-stack systems support both
- Larger address space solves IPv4 exhaustion problem
- More efficient header format improves routing performance
- Built-in security (IPsec) and better multicast support

**Subnet Mask**
- Determines which part of IP address is network, which is host
- Written in dotted decimal like IP address: `255.255.255.0`
- Binary 1s = network, binary 0s = host
- Example: `255.255.255.0` means first 24 bits are network, last 8 are host
- With this mask, `192.168.1.100` and `192.168.1.200` are on same network (can communicate directly)
- But `192.168.1.100` and `192.168.2.100` are on different networks (need router)

**CIDR Notation (Classless Inter-Domain Routing)**
- Shorthand for IP + subnet mask: `192.168.1.0/24`
- `/24` means 24 bits for network, 32-24=8 bits for host
- `/24` = 256 addresses (2^8), but first is network address, last is broadcast, leaving 254 usable
- `/32` = single host (all bits are host = just one address)
- `/16` = 65,536 addresses
- `/25` = 128 addresses
- Useful for quickly understanding network size and ranges

**IP Address Classes (Legacy)**
- Class A: `/8` - 16 million addresses per network
- Class B: `/16` - 65,536 addresses
- Class C: `/24` - 256 addresses (most common for LANs)
- Largely obsolete, replaced by CIDR, but you'll see references
- Explains why so many networks use `/24`

**Special IP Addresses**
- `127.0.0.1` (localhost) - loopback, points to your own machine
- `0.0.0.0` - "any address" (listen on all interfaces)
- `255.255.255.255` - broadcast (all devices on network)
- `10.x.x.x`, `172.16.x.x`, `192.168.x.x` - private addresses (not routable on internet)

### 3. Ports and Sockets

A port is a number that identifies a specific application or service on a computer. Combined with IP address, ports enable multiple services on one machine.

**Port Basics**
- 16-bit number: 0-65535
- Combined with IP address and protocol identifies unique connection: `192.168.1.100:80/TCP`
- Client connects to server at specific port to access service
- Multiple clients can connect to same port - server accepts multiple connections
- Kernel maintains connection state - knows which client is which

**Well-Known Ports (0-1023)**
- Reserved for standard services
- Require root/administrator privilege to bind
- `22` - SSH (Secure Shell, remote login)
- `80` - HTTP (web)
- `443` - HTTPS (encrypted web)
- `53` - DNS (domain name lookup)
- `25/587` - SMTP (sending email)
- `110/143` - POP3/IMAP (receiving email)
- `3306` - MySQL (database)
- `5432` - PostgreSQL (database)
- `6379` - Redis (cache)

**User/Ephemeral Ports (1024-65535)**
- Available for any application to use
- No privilege required
- Operating system assigns these to client connections
- Temporary - reused after connection closes
- When you browse to google.com, your browser gets assigned random ephemeral port
- Server always on well-known port, client uses ephemeral port

**Sockets**
- Endpoint of a network connection
- Combination of: IP address + port + protocol
- Example: `(192.168.1.100, 80, TCP)` is a unique socket
- In code: `socket()` creates communication endpoint, `bind()` associates with specific port
- Server socket listens on a port, client socket connects to it
- One server socket can accept multiple client sockets (one per client)

**Connection States**
- `LISTEN` - server waiting for connections on this port
- `ESTABLISHED` - active connection between client and server
- `TIME_WAIT` - connection closed but port not yet released (prevents late packets confusing new connection)
- `CLOSE_WAIT` - waiting for application to close socket

### 4. TCP vs UDP

Transport layer protocols that differ fundamentally in reliability and speed tradeoff.

**TCP (Transmission Control Protocol)**
- Connection-oriented: must establish connection before sending data
- Reliable: all packets arrive, none are lost or corrupted
- Ordered: packets arrive in same order sent
- Slow: acknowledgments and error correction add overhead
- Congestion control: slows down if network is overloaded
- Good for: Web (HTTP), email, file transfer - correctness more important than speed
- Connection flow: 3-way handshake, data transfer, graceful close
- Built-in flow control prevents sender overwhelming receiver

**UDP (User Datagram Protocol)**
- Connectionless: no handshake, just send data
- Unreliable: packets may be lost, duplicated, or arrive out of order
- Fast: minimal overhead, no acknowledgments needed
- No flow control: send as fast as you want
- Good for: DNS, video streaming, online games - speed more important than perfection
- Application handles loss: video just skips frame, game accepts missing position update
- Simpler: less CPU overhead, lower latency

**When to Use TCP**
- Files must be transferred completely (FTP)
- Data order matters (email)
- Correctness critical (banking)
- User expects reliability (web browsing)
- Bulk transfer (databases)

**When to Use UDP**
- Some loss acceptable (live video)
- Low latency critical (online games)
- Broadcast/multicast (DNS, DHCP)
- Frequent small messages where connection overhead excessive
- Real-time communication (VoIP - tolerate occasional dropped packet)

### 5. Network Interface

A network interface is the software and hardware combination that connects a computer to a network.

**Network Interface Names**
- `lo` - loopback interface (points to yourself, 127.0.0.1)
- `eth0, eth1, eth2` - ethernet interfaces (wired)
- `wlan0, wlan1` - wireless interfaces (Wi-Fi)
- `docker0, veth...` - virtual interfaces (containers, virtual machines)
- `tun0, tap0` - tunnel interfaces (VPN)
- Modern systems use predictable names: `eno1`, `ens0`, based on hardware

**MAC Address (Media Access Control)**
- 48-bit physical address: `00:1A:2B:3C:4D:5E` (hexadecimal)
- Unique identifier for network card
- Used on local network (Layer 2) to find devices
- First 3 bytes are manufacturer (OUI)
- Cannot route across internet - valid only on local network
- ARP (Address Resolution Protocol) maps IP addresses to MAC addresses

**IP Address**
- Logical address assigned to interface (can change)
- Used for routing across internet
- Interface must have IP address to communicate on network
- Can have multiple IP addresses on single interface
- Dynamic (DHCP) or static assignment
- IPv4 or IPv6

**Interface States**
- `UP` - interface is enabled and ready to send/receive
- `DOWN` - interface is disabled
- `RUNNING` - interface is actively transmitting/receiving
- `BROADCAST` - interface can broadcast (Ethernet)
- `MULTICAST` - interface can multicast

**MTU (Maximum Transmission Unit)**
- Maximum size packet can be on this interface (typically 1500 bytes)
- Packets larger than MTU are fragmented or rejected
- Lower MTU = smaller packets = more overhead
- Higher MTU = larger packets = fewer headers
- VPNs often use 1400 MTU (saves room for VPN header)

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

