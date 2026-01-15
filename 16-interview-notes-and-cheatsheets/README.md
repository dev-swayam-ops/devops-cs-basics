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

## Module Summary (Comprehensive Reference)

### 01-OS Fundamentals: Understanding System Foundations
**Core Concepts**: Kernel vs user space, processes as isolated resource containers, system resource management, virtual memory basics, hardware abstraction
**Key Mechanisms**: 
- Privilege levels (ring 0 = kernel, ring 3 = user): prevent user code from damaging system
- Process isolation: each process has own address space, preventing one process from crashing others
- Page faults: mechanism for virtual memory, laziely allocates physical memory, enables memory overcommitment
- Context switching: kernel switches between processes, overhead is real and measurable
**Critical Understanding**: OS is traffic cop managing hardware access - wouldn't work without these abstractions
**Essential Tools**: ps (list processes), top (real-time monitoring), free (memory), df (disk), strace (what process does)
**Interview Angle**: "OS abstracts hardware complexity - explain how process isolation works"

### 02-Process Management: Creating and Controlling Processes
**Core Concepts**: Process lifecycle, fork/exec pattern, signals for inter-process communication, process states, zombie processes
**Key Mechanisms**:
- fork(): creates child process, parent continues, both share resources
- exec(): replaces process image, inherits file descriptors and signals handlers
- Signals: asynchronous notifications (SIGTERM=graceful, SIGKILL=forced, SIGCHLD=child died)
- Job control: foreground/background execution, suspend (SIGSTOP), resume (SIGCONT)
**Critical Understanding**: fork+exec is how processes are created; signals are how processes communicate with OS
**Essential Tools**: pgrep (find by name), kill (send signal), jobs/fg/bg (job control), killall (kill by name)
**Interview Angle**: "How does shell create a new process? Walk through fork and exec."

### 03-Threads and Concurrency: Parallel Execution Within Process
**Core Concepts**: Threads share memory (vs processes), race conditions when multiple threads access same data, synchronization primitives prevent conflicts, deadlocks occur with improper locking
**Key Mechanisms**:
- Threads: lightweight, share heap/data, cheaper context switch than processes
- Race condition: result depends on execution order - bad
- Mutex: mutual exclusion, only one thread at a time - solves race condition
- Deadlock: circular wait (thread A waiting for B, B waiting for A)
**Trade-offs**: Threads faster but harder to debug; processes safer but heavier
**Critical Understanding**: More threads = more complexity; synchronization primitives are essential tools, not optional
**Interview Angle**: "Explain race condition and how mutex prevents it. What's a deadlock?"

### 04-Memory Management: Virtual and Physical Address Spaces
**Core Concepts**: Virtual memory abstracts physical memory, paging enables memory overcommitment, virtual addresses differ from physical, page faults trigger demand paging
**Key Mechanisms**:
- Virtual address space: each process sees 0-max address (illusion)
- Page tables: kernel maps virtual → physical pages
- Page fault: reference unmapped page → CPU interrupt → kernel allocates physical memory
- Page replacement: when physical memory full, kernel picks page to evict (LRU)
**Memory Breakdown**: Code, heap (malloc), stack (function calls), BSS (uninitialized), data (initialized)
**Metrics**: VSZ (total addressable), RSS (physical), PSS (fair share), page faults (major = disk, minor = reclaim)
**Interview Angle**: "Explain virtual memory and why programs think they have 48GB when system has 16GB"

### 05-Storage and Filesystems: Persistent Data Organization
**Core Concepts**: Hierarchical directory structure (/bin, /etc, /home, /var, /opt), filesystem types with different tradeoffs, permissions control access, inodes track file metadata
**Key Mechanisms**:
- Filesystem hierarchy: /bin (programs), /etc (config), /var (logs, temp), /home (users), /opt (optional)
- inode: contains metadata (permissions, size, timestamps), filename just pointer to inode
- Hard link: multiple names → same inode (can't cross filesystems)
- Soft link: pointer to filename (can break if target moved)
- Permissions: rwx for owner/group/others (3 bits × 3 = 9 bits = 777)
**Storage organization**: Partitions separate physical disk, each partition formatted with filesystem type
**Critical Understanding**: Hard link is direct to inode; soft link is indirect through filename
**Interview Angle**: "What's the difference between hard and soft links? Why can't hard links cross filesystems?"

### 06-Networking Fundamentals: Communication Between Systems
**Core Concepts**: OSI model layers (7 levels of abstraction), IP addressing (logical), ports (process-level), TCP (reliable), UDP (fast), network interfaces bridge software and hardware
**Key Mechanisms**:
- OSI Layers: Physical (bits) → Data Link (frames/MAC) → Network (packets/IP) → Transport (segments/TCP) → Session → Presentation → Application
- IP addressing: logical address (not MAC), identifies machine on network
- Ports: 16-bit number (0-65535) identifies service (SSH=22, HTTP=80, HTTPS=443)
- TCP: connection-oriented, reliable (retransmits), slower, ensures order
- UDP: connectionless, unreliable, fast, good for streaming/gaming
**Critical Understanding**: IP is destination-finding mechanism; TCP/UDP are delivery methods; ports identify services
**Interview Angle**: "Explain how packet travels from your laptop to Google servers - mention each layer"

### 07-DNS and HTTP Basics: Web Communication
**Core Concepts**: DNS translates names to IPs (distributed, cached), HTTP transfers documents (stateless), HTTPS encrypts with TLS, certificates prove identity
**Key Mechanisms**:
- DNS resolution: local resolver → root nameserver → TLD → authoritative (4-step journey)
- Record types: A (IPv4), AAAA (IPv6), CNAME (alias), MX (email), TXT (misc)
- HTTP methods: GET (retrieve), POST (submit), PUT (replace), DELETE (remove), PATCH (partial)
- Status codes: 2xx (success), 3xx (redirect), 4xx (client error), 5xx (server error)
- HTTPS: HTTP over TLS, encrypts content, authenticates server via certificate
- Certificate chain: End-entity cert → intermediate CA → root CA (browser trusts root)
**Critical Understanding**: DNS is distributed database; HTTPS = HTTP + encryption + authentication
**Interview Angle**: "What happens when you visit example.com? Describe DNS and HTTP steps."

### 08-Linux Kernel Basics: System Core
**Core Concepts**: Kernel is privileged code managing hardware, system calls cross privilege boundary, interrupts handle async events, kernel modules extend functionality
**Key Mechanisms**:
- Kernel architecture: process management, memory management, filesystem, networking, device drivers
- System call: user code requests kernel service (read/write/fork/exec/exit)
- Context switch: kernel switches between processes (overhead: cache miss, TLB miss)
- Interrupts: async signals from hardware (timer, network packet, disk), causes context switch
- Kernel modules: loadable code extending kernel (lsmod lists, modprobe loads)
**Critical Understanding**: Kernel is single most important piece; prevents user programs from destroying system
**Interview Angle**: "What happens when process calls read()? Explain system call mechanism."

### 09-System Calls and Signals: Kernel-Process Interface
**Core Concepts**: System calls are controlled entry points to kernel, signals are async notifications, zombie processes consume resources even after "death", signal safety matters
**Key Syscalls**:
- fork: create child process
- exec: replace program image  
- exit: terminate, become zombie until parent waits
- read/write: file I/O
- open/close: file management
**Signals**: SIGTERM (graceful shutdown), SIGKILL (forced), SIGCHLD (child changed), SIGSTOP (pause), SIGCONT (resume), SIGSEGV (segfault)
**Zombie Processes**: Process exits but parent hasn't wait()'d → still in process table → wastes resources
**Signal Safety**: Some functions not safe to call in signal handler (malloc, printf) → use signal-safe alternatives
**Critical Understanding**: Signals are how processes communicate with OS; zombies show when process cleanup isn't happening
**Interview Angle**: "What's a zombie process? How do you prevent them?"

### 10-Scheduling and Performance: Fair CPU Access
**Core Concepts**: Scheduler allocates CPU time fairly, process priority affects scheduling, load average indicates system busyness, context switches have overhead, NUMA awareness matters on large systems
**Key Mechanisms**:
- Time slices (quantum): each process gets ~10ms CPU time, then preempted
- Ready queue: processes waiting for CPU, ordered by priority
- Priority: nice value (-20 to 19), lower = higher priority (confusing!)
- Load average: processes in ready queue + running (not sleeping), indicates how busy system is
- Context switching: save registers, load new process, TLB flush = expensive
- CPU affinity: pin process to specific CPU core = better cache locality
**Scheduling algorithm**: CFS (Completely Fair Scheduler) on Linux, tries to give all processes fair share
**Critical Understanding**: Scheduler tries to be fair, but you can prioritize; load average is queue depth, not CPU%
**Interview Angle**: "How does Linux scheduler work? What do load average numbers mean?"

### 11-Distributed Systems Basics: Computing Across Networks
**Core Concepts**: Distributed systems are inherently unreliable (network can fail anytime), CAP theorem (pick 2 of 3), replication copies data, consensus ensures agreement, partitioning scales data
**Key Mechanisms**:
- CAP Theorem: Consistency (all see same data) vs Availability (always responds) vs Partition tolerance (survives network split) - pick 2
- Replication: master-slave (read scalability), master-master (write scalability), quorum (majority survives)
- Consensus: Raft (understandable), Paxos (proven), 2PC (simple but slow)
- Partitioning: range (contiguous keys) or hash (scattered), enables horizontal scaling
**Critical Understanding**: Network failures happen; systems must decide between consistency and availability
**Interview Angle**: "CAP theorem - which would you sacrifice for your system? Why?"

### 12-Databases for DevOps: Persistent Data Storage
**Core Concepts**: SQL tables vs NoSQL flexibility, ACID guarantees transaction correctness, backup/restore for disaster recovery, connection pooling for scalability
**Database Types**:
- SQL: structured schema, ACID, joins (PostgreSQL, MySQL)
- NoSQL: flexible schema, eventual consistency (MongoDB, Cassandra)
- Key-value: simple fast storage (Redis, Memcached)
- Time-series: optimized for metrics (Prometheus, InfluxDB)
**ACID Properties**: Atomicity (all or nothing), Consistency (valid state), Isolation (no interference), Durability (survives crash)
**Backup Strategies**: Full (complete copy), incremental (changes only), PITR (point-in-time recovery)
**Connections**: Pooling avoids creating new connections (expensive), maintains min/max pool size
**Interview Angle**: "Compare SQL and NoSQL. When would you choose each? What's ACID?"

### 13-Caching and Queues: Acceleration and Decoupling
**Core Concepts**: Caching accelerates reads (tradeoff: stale data), queues decouple producers/consumers (resilience), both essential for scalability
**Caching Strategies**:
- Cache-aside: app checks cache, miss → fetch from database, populate cache
- Write-through: write to cache and database synchronously
- Write-behind: write to cache immediately, eventually to database (faster but risky)
**Message Queues**: Decouple producer (submitter) from consumer (processor), resilience if consumer down
**Queue Types**: FIFO (order matters), Priority (urgent first), DLQ (dead letter, retry failed)
**Pub/Sub**: Publish message → multiple subscribers receive (broadcast)
**Cache Eviction**: LRU (least recently used), LFU (least frequently used), FIFO
**Interview Angle**: "Design a caching strategy for read-heavy workload. What are the tradeoffs?"

### 14-Security Basics: Protecting Systems and Data
**Core Concepts**: Authentication verifies identity, authorization grants permissions, encryption protects data, defense in depth uses multiple layers
**Key Mechanisms**:
- SSH keys: public key auth (no password), private key is secret
- Encryption: symmetric (fast, shared key), asymmetric (secure but slow, public/private), hashing (one-way)
- Certificates: prove domain ownership, signed by trusted CA
- File permissions: rwx for owner/group/others
- Security layers: firewall, TLS, authentication, authorization, encryption
**Critical Understanding**: No single security mechanism is sufficient; defense in depth is required
**Interview Angle**: "How do you secure an application? Describe layers of security you'd implement."

### 15-Observability and Debugging: Understanding System Behavior
**Core Concepts**: Logs record events, metrics measure values, traces follow requests, debugging tools diagnose problems
**Key Mechanisms**:
- Logs: detailed event history, expensive at scale, great for investigations
- Metrics: lightweight measurements, good for alerting and trending
- Traces: follow request through services, identify bottlenecks, only practical with sampling
- Debugging tools: strace (syscalls), ltrace (library calls), gdb (interactive), valgrind (memory)
**Log Levels**: DEBUG (dev info), INFO (milestones), WARN (degradation), ERROR (broken), FATAL (shutdown)
**Metric Types**: Gauge (current value), counter (accumulating), histogram (distribution), rate (change/time)
**Critical Understanding**: Need all three pillars; logs alone won't scale, metrics alone lack detail
**Interview Angle**: "Design monitoring for a microservices system. How would you debug slow requests?"

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

