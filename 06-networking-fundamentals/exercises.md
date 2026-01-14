# 06 - Networking Fundamentals: Exercises

## Exercise 1: Understanding OSI Model (Easy)

Research and map these to OSI layers:
- IP address routing
- TCP packet delivery
- Ethernet MAC address
- DNS resolution
- HTTP request

1. Name all 7 OSI layers
2. Which layer handles each technology above?
3. What is encapsulation?

---

## Exercise 2: Check Network Configuration (Easy)

```bash
ip addr show              # IP addresses
ip route show             # Routing table
ss -tlnp                  # Listening ports
netstat -an              # Network connections
hostname -I              # Host IP
```

1. What is your IP address?
2. What is your default gateway?
3. What ports are listening?

---

## Exercise 3: DNS Queries (Easy)

```bash
nslookup google.com
dig google.com
dig +trace google.com
host example.com
```

1. What is DNS?
2. What is the difference between A and AAAA records?
3. How many DNS queries does a lookup take?

---

## Exercise 4: Network Connectivity Testing (Medium)

```bash
ping -c 4 8.8.8.8
ping -c 4 -i 2 8.8.8.8    # Interval 2 seconds
mtr google.com             # Traceroute + ping
traceroute google.com
```

1. How many hops to reach google.com?
2. Where is latency added?
3. Interpret ICMP unreachable messages

---

## Exercise 5: Port Scanning and Services (Medium)

```bash
ss -tlnp
netstat -tulpn | grep LISTEN
nmap -p 80,443,22 localhost
lsof -i -P -n | grep LISTEN
```

1. What services are running?
2. What ports are they using?
3. Which process owns each port?

---

## Exercise 6: Packet Capture and Analysis (Medium)

```bash
sudo tcpdump -i eth0 -n port 80
sudo tcpdump -r capture.pcap
sudo tshark -r capture.pcap
```

1. What does tcpdump do?
2. Capture HTTP traffic and analyze
3. What TCP flags appear in initial handshake?

---

## Exercise 7: Routing and Network Performance (Medium)

```bash
ip route show
ip route add 192.168.1.0/24 via 10.0.0.1
traceroute -m 15 google.com
mtr -c 10 google.com
```

1. What is routing?
2. How does traffic choose a path?
3. What is a routing loop?

---

## Exercise 8: Network Interfaces and Bonding (Medium)

```bash
ip link show
ip addr show
ethtool eth0
```

1. What is an interface?
2. What is link speed?
3. How would you bond two interfaces?

---

## Exercise 9: Firewall and NAT Basics (Medium)

```bash
sudo iptables -L -n
sudo iptables -L -n -t nat
sudo firewall-cmd --list-all  # firewalld
```

1. What is a firewall rule?
2. What is NAT (Network Address Translation)?
3. How to block a port with iptables?

---

## Exercise 10: Network Debugging and Monitoring (Medium)

```bash
iftop -n          # Top network talkers
nethogs           # Network usage by process
vnstat -h         # Historical network stats
ss -an           # All connections
netstat -an | grep ESTABLISHED | wc -l
```

1. Which process uses most bandwidth?
2. How many active connections?
3. Identify unusual traffic

---
