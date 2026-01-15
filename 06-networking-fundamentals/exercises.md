# 06 - Networking Fundamentals Exercises

## Easy Exercises (1-5)

### Exercise 1: List Network Interfaces
**Objective**: See available network devices.

**Task**:
- Run: `ip link show`
- How many interfaces?
- Which is loopback?
- Which is primary (eth0, wlan0)?
- What's the state? (UP, DOWN?)

---

### Exercise 2: Check IP Addresses
**Objective**: Learn IP configuration.

**Task**:
- Run: `ip addr show`
- What's your IP?
- What's subnet (/24, /32)?
- What's loopback IP? (always?)
- What's broadcast address?

---

### Exercise 3: View Routing Table
**Objective**: Understand how traffic routes.

**Task**:
- Run: `ip route show`
- What's default gateway?
- What's local route?
- Destination 0.0.0.0 means?
- Which interface used?

---

### Exercise 4: Test Connectivity
**Objective**: Verify network works.

**Task**:
- Run: `ping -c 4 127.0.0.1` (localhost)
- All packets received?
- Latency? (ms)
- Run: `ping -c 4 8.8.8.8`
- Can reach internet?

---

### Exercise 5: Check Open Ports
**Objective**: See listening services.

**Task**:
- Run: `ss -tuln | grep LISTEN`
- What ports are open?
- Which protocol? (tcp, udp)
- What's 127.0.0.1 mean? (localhost only)
- What's 0.0.0.0 mean? (all interfaces)

---

## Medium Exercises (6-10)

### Exercise 6: Resolve DNS
**Objective**: Understand name resolution.

**Task**:
- Run: `nslookup google.com`
- What's IP for google.com?
- Run: `nslookup localhost`
- What's IP for localhost?
- Check resolver: `cat /etc/resolv.conf`
- What nameservers configured?

---

### Exercise 7: Trace Route to Server
**Objective**: See path to destination.

**Task**:
- Run: `traceroute 8.8.8.8`
- How many hops?
- What's first hop? (default gateway)
- What's latency trend? (increasing?)
- Run: `traceroute google.com`
- Same as IP or different?

---

### Exercise 8: Analyze Network Traffic
**Objective**: Monitor connection patterns.

**Task**:
- Run: `ss -tuln`
- Count listening sockets
- Run: `ss -tan`
- Show established connections
- What's difference? (state column)

---

### Exercise 9: Check Interface Statistics
**Objective**: See traffic patterns.

**Task**:
- Run: `ip -s link show`
- RX packets > TX packets?
- Any errors? (ERR column)
- Run: `ifconfig`
- Same info different format?

---

### Exercise 10: Calculate IP Subnet
**Objective**: Understand CIDR notation.

**Task**:
- IP: 192.168.1.0/24
- How many hosts? (2^(32-24)-2)
- Network address?
- Broadcast address?
- IP: 10.0.0.0/16
- How many hosts? (2^(32-16)-2)
- Range for /16?

---

## Submission Tips

1. Use `ip` commands (modern, replaces ifconfig)
2. `ss` is newer than `netstat`
3. Remember: 0.0.0.0 = all interfaces
4. Remember: 127.0.0.1 = localhost
5. CIDR: /24 = 256 addresses, /16 = 65536 addresses
