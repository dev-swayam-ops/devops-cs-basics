# 16 - Interview Notes and Cheatsheets

## What You'll Learn

- Key concepts from all 15 modules
- Common DevOps interview questions
- System design thinking
- Quick reference guides
- Best practices summary
- Technical depth for each topic

## Prerequisites

- Completed **15-observability-and-debugging**
- Understanding of all 15 modules
- Ready for interviews or deep dives

## Module Summary

### 01-OS Fundamentals
- System resources (CPU, memory, disk)
- Process vs kernel space
- Key tools: ps, top, free, df

### 02-Process Management
- Process lifecycle and signals
- Process states (running, sleeping, zombie)
- Job control and background execution

### 03-Threads and Concurrency
- Thread basics and synchronization
- Race conditions and deadlocks
- Synchronization primitives (locks, semaphores)

### 04-Memory Management
- Virtual vs physical memory
- Paging and page faults
- Memory metrics (VSZ, RSS, PSS)

### 05-Storage and Filesystems
- Filesystem hierarchy and types
- Disk partitions and mounting
- File permissions and inodes
- Hard links vs soft links

### 06-Networking Fundamentals
- OSI model and TCP/IP
- IP addressing and subnetting
- DNS and routing
- Network interfaces and ports

### 07-DNS and HTTP Basics
- DNS resolution and record types
- HTTP methods and status codes
- HTTPS and SSL/TLS certificates
- Request/response structure

### 08-Linux Kernel Basics
- Kernel architecture
- System calls and interrupts
- Kernel modules (lsmod, modprobe)
- Kernel parameters (sysctl)

### 09-System Calls and Signals
- Common syscalls (fork, exec, read, write)
- Signal types and handling
- Signal trapping and zombie processes
- Process termination

### 10-Scheduling and Performance
- CPU scheduling and time slices
- Process priorities (nice/renice)
- Load average interpretation
- Context switching overhead

### 11-Distributed Systems Basics
- CAP theorem
- Replication and consensus
- Quorum-based systems
- Service discovery and load balancing

### 12-Databases for DevOps
- SQL vs NoSQL
- ACID properties
- Backup and restore strategies
- Connection pooling and monitoring

### 13-Caching and Queues
- Cache strategies and patterns
- Redis operations and persistence
- Message queues and pub/sub
- LRU eviction and TTL

### 14-Security Basics
- SSH keys and authentication
- File permissions and ownership
- Encryption and certificates
- Firewall rules and access control

### 15-Observability and Debugging
- Logging and log levels
- Metrics and monitoring
- Tracing and profiling
- Tools (strace, ltrace, gdb)

## Common Interview Questions

### Architecture & Design
- "Design a scalable system for X million users"
- "How would you handle 10,000 requests/second?"
- "Describe a microservices architecture"
- "What's your approach to disaster recovery?"

### Operations & DevOps
- "How do you debug a slow application?"
- "What's your monitoring strategy?"
- "How do you handle database backups?"
- "Describe your deployment process"

### System Knowledge
- "Explain the difference between processes and threads"
- "What happens when you type a command in shell?"
- "How does memory management work?"
- "What are the differences between TCP and UDP?"

### Troubleshooting
- "How do you find memory leaks?"
- "What do you do when a service is down?"
- "How do you optimize database queries?"
- "How would you handle network latency?"

## Key Concepts Quick Ref

### Performance Metrics
```
CPU: load average, context switches, CPU%
Memory: VSZ, RSS, PSS, page faults
Disk: I/O rate, latency, throughput
Network: latency, bandwidth, packet loss
```

### Reliability Patterns
```
Redundancy: Multiple instances
Failover: Automatic switching
Replication: Data copies
Backup: Point-in-time recovery
Monitoring: Alert on anomalies
```

### Scalability Strategies
```
Vertical: More power per machine
Horizontal: More machines
Caching: Reduce database load
Sharding: Distribute data
Load balancing: Distribute requests
```

## Essential Commands Quick Ref

```bash
# System info
uname -a; uptime; free -h; df -h

# Processes
ps aux; top; pgrep; killall

# Networking
ip addr; ip route; netstat; ss; ping; traceroute

# Logs
journalctl -f; tail -f; grep

# Debugging
strace; ltrace; gdb; valgrind

# Files
chmod; chown; ls -la; stat; find

# SSH
ssh-keygen; ssh-copy-id; ssh -i key

# Database
psql; mysql; redis-cli; mongosh

# Performance
time; perf; iotop; vmstat; iostat
```

## Best Practices

### Code & Development
- Write idempotent code
- Use version control (git)
- Automated testing
- Code review process

### Infrastructure
- Infrastructure as code (IaC)
- Immutable infrastructure
- Blue-green deployments
- Disaster recovery plan

### Operations
- Monitoring and alerting
- Centralized logging
- Documentation
- Runbooks for common issues

### Security
- Least privilege
- Encryption in transit and at rest
- Regular security updates
- Secret management

## Concepts by Difficulty

### Beginner
- Process hierarchy (ps tree)
- File permissions (chmod/chown)
- Basic networking (ping, DNS)
- SSH keys
- Log files

### Intermediate
- Process states and scheduling
- Memory management and paging
- Network protocols (TCP/IP)
- Database transactions
- Caching strategies

### Advanced
- Distributed consensus
- Kernel internals
- Performance optimization
- Security hardening
- System design

## Interview Preparation Tips

1. **Explain fundamentals first**: Start simple, add complexity
2. **Use examples**: Real-world scenarios
3. **Show tradeoffs**: No perfect solutions
4. **Ask clarifying questions**: Don't assume
5. **Discuss monitoring**: How would you know if it works?
6. **Consider failure modes**: What if X breaks?
7. **Propose metrics**: How to measure success?

## Recommended Resources

- Linux: "How Linux Works" book
- Networking: "Computer Networks" (Kurose & Ross)
- Distributed Systems: "Designing Data-Intensive Applications"
- DevOps: "The Phoenix Project" (case study)
- Security: OWASP Top 10, CIS Benchmarks

## Next Steps After This Course

1. Build small projects using each module
2. Contribute to open-source infrastructure projects
3. Get certifications (LFCE, CKA)
4. Join DevOps communities
5. Stay current with industry trends

