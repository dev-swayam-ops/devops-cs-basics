# 08-16: Compact Solutions & Resources

## Module 08 - Linux Kernel: Solutions/Cheatsheet

Boot sequence: BIOS → GRUB → Kernel → Init (systemd)
Kernel parameters: `/proc/sys/` or `sysctl`
Debug: `dmesg`, `journalctl`

---

## Module 09 - System Calls and Signals: Exercises & Solutions

**Exercises**: 
1. System call types: fork, exec, read, write, open, close
2. Common signals: SIGTERM, SIGKILL, SIGUSR1
3. Signal handlers with trap
4. Syscall count with strace
5. Child process handling
6. Signal masks
7. Real-time signals
8. Signal-safe functions
9. Process communication
10. Signal debugging

**Solutions**: Signals = kernel-to-process communication. SIGKILL can't be caught. Trap handlers for custom behavior.

**Quiz**: SIGTERM=15 (graceful), SIGKILL=9 (force), trap handles signals

---

## Module 10 - Scheduling and Performance: Exercises

1. CPU scheduling algorithms (FIFO, Round-Robin, CFS)
2. Load average understanding
3. Process priorities (nice/renice)
4. CPU affinity (taskset)
5. Context switch overhead
6. Load balancing
7. Latency vs throughput
8. Performance profiling (perf)
9. Bottleneck identification
10. Optimization techniques

**Solutions**: CFS default scheduler, nice -20 to +19, perf stat/record for profiling

**Commands**: `nice`, `renice`, `taskset`, `perf stat`, `stress-ng`

---

## Module 11 - Distributed Systems: Exercises

1. CAP theorem (Consistency, Availability, Partition tolerance)
2. Consensus algorithms (Raft, Paxos)
3. Replication strategies
4. Consistency models (Eventual, Strong)
5. Two-phase commit
6. Byzantine failures
7. Failure detection (heartbeat)
8. Leader election
9. Split-brain scenarios
10. Distributed tracing

**Key Concepts**: CAP=pick2, consistency hard in distributed systems, consensus essential

---

## Module 12 - Databases for DevOps: Exercises

1. SQL vs NoSQL trade-offs
2. ACID properties
3. Indexing strategies
4. Query optimization
5. Replication and failover
6. Backup and recovery
7. Connection pooling
8. Sharding strategies
9. Monitoring and alerting
10. Capacity planning

**Commands**: `mysqldump`, `mongodump`, `EXPLAIN`, `ANALYZE TABLE`

---

## Module 13 - Caching and Queues: Exercises

1. Cache invalidation strategies
2. Redis data structures
3. Memcached vs Redis
4. Queue systems (RabbitMQ, Kafka)
5. Cache warming
6. TTL and expiration
7. Cache stampede prevention
8. Distributed caching
9. Message ordering
10. Dead-letter queues

**Tools**: Redis, Memcached, RabbitMQ, Kafka

---

## Module 14 - Security Basics: Exercises

1. Authentication (password, 2FA, OAuth)
2. Encryption (symmetric, asymmetric, hashing)
3. TLS/SSL certificates
4. Vulnerability scanning
5. Security hardening
6. Firewall rules
7. SELinux/AppArmor
8. Access control lists
9. Audit logging
10. Incident response

**Best Practices**: Principle of least privilege, defense in depth, regular updates

---

## Module 15 - Observability & Debugging: Exercises

1. Logging architecture (ELK stack)
2. Metrics collection (Prometheus)
3. Distributed tracing (Jaeger)
4. Alerting and thresholds
5. Performance profiling
6. Log analysis
7. Correlation IDs
8. SLI/SLO definition
9. Root cause analysis
10. Chaos engineering

**Tools**: ELK, Prometheus, Grafana, Jaeger, OpenTelemetry

---

## Module 16 - Interview Notes: Key Topics

**OS/Kernel**:
- Virtual memory, paging, segmentation
- Context switching, scheduling algorithms
- System calls, privilege levels

**Processes & Threads**:
- Process creation (fork/exec)
- Thread safety, race conditions, deadlock
- Synchronization primitives

**Memory**:
- Heap vs Stack
- Memory leaks, garbage collection
- Cache locality

**Networking**:
- TCP/IP stack, 3-way handshake
- DNS resolution, HTTP status codes
- Load balancing, CDN concepts

**Storage**:
- Inode structure, hard vs soft links
- RAID levels, filesystem performance
- Backup strategies

**Distributed Systems**:
- CAP theorem, consistency models
- Consensus algorithms
- Replication, failover

**Databases**:
- ACID properties, indexing
- Query optimization, sharding
- Replication strategies

**Security**:
- Encryption, authentication
- TLS handshake
- Vulnerability assessment

**DevOps Skills**:
- Infrastructure as Code
- Container orchestration
- CI/CD pipelines
- Monitoring and alerting
- Disaster recovery planning

---

**Interview Patterns**:
- "Tell me about a difficult production incident"
- System design questions (scale a service)
- Explain a technical concept in simple terms
- Trade-off analysis (speed vs consistency)
- Problem-solving approach

**Preparation**:
- Study fundamentals deeply
- Practice system design
- Review past projects
- Prepare specific examples
- Know your tools well

---
