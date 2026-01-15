# 16 - Interview Notes and Cheatsheets - Reference

## Module Progression Map

```
01 OS Fundamentals
  ↓
02 Process Management ← 03 Threads
  ↓
04 Memory Management ← 05 Storage
  ↓
06 Networking ← 07 DNS/HTTP ← 08 Kernel
  ↓
09 Signals ← 10 Scheduling
  ↓
11 Distributed Systems ← 12 Databases
  ↓
13 Caching ← 14 Security
  ↓
15 Observability → 16 Interview Notes
```

## Essential Concepts by Module

### 01: OS Fundamentals
- CPU, memory, disk interaction
- Commands: ps, top, free, df, du

### 02: Processes
- States: ready, running, blocked, zombie
- Commands: ps, jobs, bg, fg, kill

### 03: Threads
- Synchronization primitives
- Race conditions, deadlock
- Commands: pthreads (C), threading (Python)

### 04: Memory
- Virtual vs physical, paging
- Metrics: VSZ, RSS, PSS
- Commands: free, cat /proc/meminfo, pmap

### 05: Storage
- Filesystem types: ext4, xfs
- Permissions: rwx, chmod, chown
- Commands: lsblk, df, du, chmod, chown

### 06: Networking
- OSI model, TCP/IP, IP subnetting
- Ports and sockets
- Commands: ip, ss, ping, traceroute

### 07: DNS/HTTP
- DNS records: A, MX, CNAME
- HTTP methods, status codes
- Commands: nslookup, dig, curl

### 08: Kernel
- Modules, system calls, parameters
- Commands: lsmod, uname, sysctl, dmesg

### 09: Signals
- Signal types (SIGTERM, SIGKILL)
- Signal handling and trapping
- Commands: kill, trap, signal

### 10: Scheduling
- Process priorities (nice)
- Load average, context switches
- Commands: uptime, nice, renice, top

### 11: Distributed Systems
- CAP theorem, replication, consensus
- Quorum, leader election
- Concepts: Raft, eventual consistency

### 12: Databases
- SQL vs NoSQL, ACID
- Backup/restore, replication
- Tools: psql, mysql, mongosh

### 13: Caching
- Cache strategies, Redis
- Pub/Sub, queues
- Commands: redis-cli, SET, GET

### 14: Security
- SSH keys, permissions
- Encryption, certificates
- Commands: ssh-keygen, chmod, openssl

### 15: Observability
- Logs, metrics, traces
- Debugging tools
- Commands: journalctl, strace, top

## Interview Question Categories

### System Design
- "Design a load balanced system"
- "How would you handle 1M requests/sec"
- "Design a multi-region architecture"

### Troubleshooting
- "Application is slow, what do you check?"
- "Server is out of disk space"
- "Database queries are timing out"

### Architecture
- "Microservices vs monolith?"
- "Stateful vs stateless services?"
- "How to handle data consistency?"

### Operations
- "What's your backup strategy?"
- "How do you deploy without downtime?"
- "How do you handle secrets?"

### Security
- "How do you secure infrastructure?"
- "What's your SSH strategy?"
- "How do you handle credentials?"

## Red Flags in Interviews

```
❌ "I don't know" without exploration
❌ "It depends" without explaining why
❌ Hardcoded credentials or secrets
❌ Single point of failure design
❌ No monitoring or alerting
❌ No disaster recovery plan
❌ Ignoring security considerations
```

## Green Flags

```
✓ Ask clarifying questions
✓ Discuss tradeoffs
✓ Mention failure modes
✓ Show monitoring approach
✓ Mention testing strategy
✓ Discuss cost implications
✓ Propose metrics for success
```

## Skill Matrix

| Skill | Beginner | Intermediate | Advanced |
|-------|----------|--------------|----------|
| Linux | Basic commands | Scripting, troubleshooting | Kernel tuning |
| Networks | IP, DNS | TCP/IP, routing | BGP, QoS |
| Databases | SQL basics | Transactions, indexing | Sharding, optimization |
| DevOps | Manual deploys | CI/CD pipelines | Full IaC, GitOps |
| Security | Basic SSH | Encryption, certs | Compliance, hardening |

## Time Complexity Quick Ref

| Operation | Best | Average | Worst |
|-----------|------|---------|-------|
| Array search | O(1) | O(n) | O(n) |
| Hash lookup | O(1) | O(1) | O(n) |
| BST search | O(1) | O(log n) | O(n) |
| Sorting | O(n) | O(n log n) | O(n²) |
| Consistent hashing | O(1) | O(1) | O(1) |

## Performance Rule of Thumb

```
Optimize for:
1. Correctness (always first)
2. Readability (maintainability)
3. Performance (if needed, measure first)

Don't:
- Optimize prematurely
- Ignore logging
- Skip error handling
- Forget about monitoring
```

## Common Mistakes

| Mistake | Impact | Prevention |
|---------|--------|-----------|
| No monitoring | Blind to issues | Implement early |
| Single point of failure | Total outage | Redundancy |
| Hardcoded secrets | Security breach | Vault/env vars |
| No backups | Data loss | Automated backups |
| No testing | Broken deployments | CI/CD tests |
| Poor documentation | Operational issues | README + runbooks |

## Technical Depth Areas

### Choose 2-3 to specialize in:

1. **Systems**: OS, kernel, performance
2. **Networking**: Protocols, security, routing
3. **Databases**: SQL, NoSQL, optimization
4. **Security**: Encryption, access control, hardening
5. **DevOps**: Infrastructure, CI/CD, containers
6. **Distributed Systems**: Consensus, scalability
7. **Performance**: Profiling, optimization, caching

## Final Checklist Before Interview

```
Knowledge:
☐ All 15 modules reviewed
☐ Can explain each concept
☐ Know tool for each task
☐ Understand tradeoffs

Practice:
☐ Hands-on labs completed
☐ Exercises solved
☐ Designed a system
☐ Debugged a real issue

Soft Skills:
☐ Can explain clearly
☐ Ask clarifying questions
☐ Admit when uncertain
☐ Show curiosity
☐ Listen actively
```

## Quick Reference Links

- Linux: man pages, GNU Coreutils
- Networking: RFCs, OSI model diagram
- Databases: Official documentation
- DevOps: CNCF Landscape, Awesome lists
- Security: OWASP, CIS Benchmarks
