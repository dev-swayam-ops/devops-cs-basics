# 16 - Interview Notes and Cheatsheets

## What You'll Learn

Synthesize all previous modules for:
- Technical interview preparation
- System design thinking
- Problem-solving frameworks
- Common DevOps interview questions
- Architecture design patterns
- Performance optimization strategies
- Troubleshooting methodology

## Prerequisites

- Completed **01-15 all modules**
- Understanding of all CS fundamentals
- Real-world systems experience

## Key Concepts for Interviews

### 1. System Design Framework
```
1. Clarify Requirements
   - Functional: What does system do?
   - Non-functional: Scale, latency, availability

2. Estimate Capacity
   - Users, QPS, data size
   - Back-of-envelope calculations

3. Design Core Components
   - APIs, databases, caches, queues
   - Choose appropriate technologies

4. Scale for Load
   - Horizontal scaling (more servers)
   - Vertical scaling (bigger servers)
   - Database replication, sharding
   - Caching layers

5. Handle Failures
   - Redundancy, failover
   - Circuit breakers, retries
   - Health checks, monitoring
```

### 2. Common Interview Topics

#### OS and Processes
- Difference between process and thread
- How fork() and exec() work
- Process states and lifecycle
- IPC mechanisms
- Signals and termination

#### Memory and Storage
- Virtual vs physical memory
- Paging and page faults
- Filesystem structure and inodes
- Hard links vs soft links
- RAID levels and backups

#### Networking
- OSI model and TCP/IP stack
- TCP vs UDP trade-offs
- DNS resolution process
- Load balancing strategies
- Firewall and security

#### Distributed Systems
- CAP theorem and trade-offs
- Consensus algorithms (Raft, Paxos)
- Replication and consistency models
- Eventual consistency concepts
- Failure scenarios and recovery

#### Databases
- SQL vs NoSQL trade-offs
- Indexing and query optimization
- Replication and sharding
- ACID transactions
- Connection pooling

#### Performance
- Load average and CPU scheduling
- Context switching overhead
- Cache locality and thrashing
- Profiling and bottleneck identification
- Optimization techniques

### 3. Problem-Solving Methodology
```
1. Read problem carefully
2. Ask clarifying questions
3. State assumptions
4. Draw diagrams
5. Start simple, add complexity
6. Discuss trade-offs
7. Code or explain implementation
8. Test and refine
```

## Common Interview Questions

### System Design
1. **Design a URL shortener**
   - Requirements: Shorten URLs, redirect, analytics
   - Design: API, hash function, database, cache
   - Scale: Sharding by user ID, caching, CDN

2. **Design a real-time messaging system**
   - Requirements: Delivery, ordering, notifications
   - Design: Message queue, database, WebSocket
   - Scale: Partitioning, consumer groups, retention

3. **Design a distributed cache**
   - Requirements: Low latency, high throughput
   - Design: Consistent hashing, replication
   - Scale: Node failover, eviction policies

4. **Design a social media feed**
   - Requirements: Personalized, real-time, scalable
   - Design: Denormalization, cache, message queue
   - Scale: Fanout, sharding by user ID

### Technical Questions

**Q: Why is load average 4 on single-core system bad?**
A: Load of 4 means 4 processes ready to run but only 1 CPU. 3 processes waiting = high latency. Healthy is load < core count.

**Q: How does kernel handle millions of file descriptors?**
A: File descriptors are just indices into process's file table. Kernel uses hash tables or arrays. Limit set by /proc/sys/fs/file-max and ulimit -n.

**Q: What's the difference between hard and soft links?**
A: Hard link = same inode, increments link count. Can't cross filesystems. Soft link = just a path, can break if target deleted.

**Q: When would you choose UDP over TCP?**
A: UDP when speed > reliability: video streaming, voice calls, online gaming. Low latency more important than occasional packet loss.

**Q: How does DNS caching work?**
A: Local resolver caches results for TTL seconds. No lookup needed until cache expires. ISP resolver caches too. Can cause stale data.

**Q: Explain the CAP theorem**
A: Pick 2 of 3: Consistency (all nodes same), Availability (always responsive), Partition tolerance (survives split). Real systems must handle partitions, so choose between CA and AP.

## Hands-on Interview Practice

```bash
# 1. Design exercise: Implement cache
# - What's eviction policy? (LRU)
# - How handle concurrent access? (Locks)
# - TTL implementation? (Background thread)

# 2. Architecture question
# - Design web app with 1M users
# - Load balancer, app servers, database replicas
# - Caching layer (Redis)
# - Message queue (RabbitMQ)
# - Monitoring (Prometheus + Grafana)

# 3. Debugging scenario
# - Application responding slow
# - Check: CPU usage, memory, disk I/O
# - Use: top, iostat, perf
# - Profile hot functions
# - Identify bottleneck

# 4. Performance optimization
# - Reduce database calls (caching, batching)
# - Index frequently queried fields
# - Connection pooling
# - Query optimization (EXPLAIN PLAN)
```

## Interview Tips

1. **Listen carefully**: Understand requirements before designing
2. **Ask questions**: Clarify ambiguities
3. **Think out loud**: Show your thought process
4. **Draw diagrams**: Helps visualize and communicate
5. **Discuss trade-offs**: No perfect solution, explain choices
6. **Start simple**: Basic design, then add complexity
7. **Handle failures**: Mention redundancy, monitoring, alerting
8. **Calculate numbers**: Show you can estimate scale
9. **Know your tools**: Can name appropriate tech for each layer
10. **Be honest**: "I don't know but would learn" is better than guessing

## Resources for Each Module

### 01-02: OS & Processes
- Book: "Operating Systems: Three Easy Pieces"
- Practice: Explain fork/exec, process states, signals

### 03-04: Concurrency & Memory
- Book: "The Little Book of Semaphores"
- Practice: Mutex, deadlock scenarios, memory layout

### 05-06: Storage & Networking
- Topics: RAID levels, OSI model, TCP/IP
- Practice: Design disk backup strategy, network troubleshooting

### 07-08: DNS/HTTP & Kernel
- Topics: DNS hierarchy, HTTP methods, kernel subsystems
- Practice: Trace DNS lookup, understand system calls

### 09-10: Syscalls & Performance
- Topics: Context switches, load average, profiling
- Practice: strace analysis, perf profiling

### 11-12: Distributed Systems & Databases
- Book: "Designing Data-Intensive Applications"
- Practice: Design replication, handle failures

### 13-14: Caching, Queues & Security
- Topics: Cache invalidation, queue patterns, encryption
- Practice: Design cache layer, secure communication

### 15: Observability
- Topics: Logs, metrics, traces
- Practice: Debug production issues, write queries

## Next Steps After Interviews

1. **Build projects**: Create real systems using learned concepts
2. **Read papers**: CAP theorem, Raft, Google Bigtable
3. **Contribute to OSS**: Real-world experience
4. **Learn container tech**: Docker, Kubernetes
5. **Study cloud platforms**: AWS, GCP, Azure
6. **Practice system design**: LeetCode, System Design Primer
7. **Join DevOps community**: GitHub, Reddit, local meetups

## Additional Resources

- System Design Primer: https://github.com/donnemartin/system-design-primer
- Grokking System Design: https://www.educative.io/courses/grokking-the-system-design-interview
- LeetCode: https://leetcode.com/
- GeeksforGeeks: https://www.geeksforgeeks.org/
- InterviewBit: https://www.interviewbit.com/

