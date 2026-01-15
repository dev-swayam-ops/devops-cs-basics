# 06 - Networking Fundamentals Solutions

## Easy Solutions (1-5)

### Solution 1: List Network Interfaces

**Command**:
```bash
ip link show
```

**Output Example**:
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
2: eth0: <BROADCAST,RUNNING,MULTICAST> mtu 1500
3: docker0: <BROADCAST,MULTICAST> mtu 1500
```

**Answers**:
- Interfaces: 3
- Loopback: lo (index 1)
- Primary: eth0 (broadcast)
- State: UP, RUNNING (active)

---

### Solution 2: Check IP Addresses

**Command**:
```bash
ip addr show
```

**Output Example**:
```
1: lo: inet 127.0.0.1/8
2: eth0: inet 192.168.1.100/24 brd 192.168.1.255
3: docker0: inet 172.17.0.1/16
```

**Answers**:
- Your IP: 192.168.1.100
- Subnet: /24 (256 total, 254 usable)
- Loopback: 127.0.0.1/8 (always)
- Broadcast: 192.168.1.255

---

### Solution 3: View Routing Table

**Command**:
```bash
ip route show
```

**Output Example**:
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel
127.0.0.0/8 dev lo proto kernel
```

**Answers**:
- Default gateway: 192.168.1.1
- Local route: 192.168.1.0/24
- 0.0.0.0 means: default (match anything)
- Interface: eth0 (or lo for loopback)

---

### Solution 4: Test Connectivity

**Commands**:
```bash
ping -c 4 127.0.0.1
ping -c 4 8.8.8.8
```

**Output Example**:
```
From 127.0.0.1:
4 packets transmitted, 4 received, 0% loss, time 3ms
```

**Answers**:
- Localhost: 100% success (always)
- Internet: depends (usually yes)
- Latency: <1ms localhost, 10-50ms internet

---

### Solution 5: Check Open Ports

**Command**:
```bash
ss -tuln | grep LISTEN
```

**Output Example**:
```
tcp   LISTEN 0 128 0.0.0.0:22 0.0.0.0:*
tcp   LISTEN 0 128 127.0.0.1:5432 0.0.0.0:*
tcp   LISTEN 0 128 192.168.1.100:80 0.0.0.0:*
```

**Analysis**:
- Ports: 22 (SSH), 5432 (DB), 80 (HTTP)
- 0.0.0.0 = listening on all interfaces
- 127.0.0.1 = localhost only (internal)
- TCP vs UDP: Protocol type

---

## Medium Solutions (6-10)

### Solution 6: Resolve DNS

**Commands**:
```bash
nslookup google.com
nslookup localhost
cat /etc/resolv.conf
```

**Output Example**:
```
google.com:
Name: google.com
Address: 142.251.32.14

localhost:
Name: localhost
Address: 127.0.0.1

/etc/resolv.conf:
nameserver 8.8.8.8
nameserver 8.8.4.4
```

**Answers**:
- Google: 142.251.32.14 (changes)
- Localhost: 127.0.0.1 (always)
- Resolvers: 8.8.8.8 (Google DNS)

---

### Solution 7: Trace Route

**Command**:
```bash
traceroute 8.8.8.8
```

**Output Example**:
```
1  router (192.168.1.1)  2ms
2  isp-gw (203.0.113.1)  10ms
3  backbone (198.51.100.1)  20ms
4  google (8.8.8.8)  25ms
```

**Analysis**:
- Hops: 4 to destination
- First: gateway (local network)
- Latency: increases toward destination
- DNS resolution: name if available, else IP

---

### Solution 8: Analyze Network Traffic

**Commands**:
```bash
ss -tuln        # Listening
ss -tan         # All TCP
ss -uan         # All UDP
```

**Output Example**:
```
LISTEN sockets: 5
tcp LISTEN 0 128 0.0.0.0:22
tcp LISTEN 0 128 0.0.0.0:80

Established: 2
tcp ESTAB 0 0 192.168.1.100:50234 142.251.32.14:443
```

---

### Solution 9: Check Interface Statistics

**Command**:
```bash
ip -s link show eth0
```

**Output Example**:
```
RX: bytes packets errs drop fifo frame
    504000 5000   0    0    0    0
TX: bytes packets errs drop fifo colls
    302000 3000   0    0    0    0
```

**Analysis**:
- RX (receive) > TX (send): downloading
- No errors: healthy interface
- Same info as `ifconfig` but clearer

---

### Solution 10: Calculate IP Subnet

**Calculation for 192.168.1.0/24**:
```
/24 means 24 network bits, 8 host bits
2^8 = 256 total addresses
256 - 2 = 254 usable (minus network and broadcast)

192.168.1.0/24:
- Network: 192.168.1.0
- First host: 192.168.1.1
- Last host: 192.168.1.254
- Broadcast: 192.168.1.255
```

**Calculation for 10.0.0.0/16**:
```
/16 means 16 network bits, 16 host bits
2^16 = 65536 addresses
65536 - 2 = 65534 usable

10.0.0.0/16:
- Range: 10.0.0.0 to 10.0.255.255
```

---

## Quick Reference

```bash
# Interface info
ip addr show; ip route show

# Connectivity
ping -c 4 8.8.8.8

# Open ports
ss -tuln

# DNS resolution
nslookup google.com

# Route analysis
traceroute 8.8.8.8
```
