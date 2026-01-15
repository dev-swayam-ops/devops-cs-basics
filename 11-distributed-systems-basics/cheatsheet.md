# 11 - Distributed Systems Basics Cheatsheet

## Core Concepts

| Concept | Definition | Example |
|---------|-----------|---------|
| Node | Single machine in system | Server, database |
| Replica | Copy of data | Backup database |
| Partition | Data or network split | Network failure |
| Quorum | Majority vote (N/2+1) | 3 of 5 nodes |
| Consensus | Agreement on state | Raft, Paxos |
| Heartbeat | Liveness signal | Periodic ping |
| Leader | Coordinator node | Master in master-slave |

## CAP Theorem

| Type | Consistency | Availability | Tolerance | Example |
|------|-------------|--------------|-----------|---------|
| CA | ✓ | ✓ | ✗ | Single machine |
| CP | ✓ | ✗ | ✓ | PostgreSQL |
| AP | ✗ | ✓ | ✓ | DNS, cache |

## Replication Strategies

| Strategy | Pros | Cons | Consistency |
|----------|------|------|-------------|
| Master-Slave | Simple | Single write point | Strong |
| Master-Master | Highly available | Conflicts | Eventual |
| Quorum-based | Balanced | Complex | Tunable |

## Consensus Algorithms

| Algorithm | Complexity | Use Case | Tolerance |
|-----------|-----------|----------|-----------|
| Raft | Medium | General | ⌊(N-1)/2⌋ |
| Paxos | High | Google/Yahoo | ⌊(N-1)/2⌋ |
| Two-phase commit | Simple | Traditional DB | Limited |

## Fault Tolerance Calculation

| Total Nodes | Quorum | Can tolerate failures |
|------------|--------|----------------------|
| 1 | 1 | 0 |
| 3 | 2 | 1 |
| 5 | 3 | 2 |
| 7 | 4 | 3 |
| 9 | 5 | 4 |

Formula: tolerate ⌊(N-1)/2⌋ failures

## Service Discovery

| Component | Purpose | Example |
|-----------|---------|---------|
| Registry | Store service locations | Consul, etcd |
| Health check | Monitor service status | Ping, HTTP GET |
| Load balancer | Distribute requests | HAProxy, nginx |
| DNS | Name resolution | Route to services |

## Consistency Models

| Model | Guarantee | Speed | Use |
|-------|-----------|-------|-----|
| Strong | All see same immediately | Slow | Banking |
| Eventual | Consistency after delay | Fast | Social |
| Causal | Cause-effect order | Medium | Messages |

## Network Partition Handling

| Scenario | Available? | Consistent? | Action |
|----------|-----------|------------|--------|
| Quorum present | Yes | Yes | Accept writes |
| No quorum | No | Yes | Reject writes |
| Both partitions | Partial | Partial | Split-brain risk |
| Healed | Yes | Yes | Reconcile |

## Distributed System Patterns

| Pattern | Problem | Solution |
|---------|---------|----------|
| Leader election | Coordinator needed | Raft/Paxos |
| Sharding | Data too large | Partition by key |
| Replication | Fault tolerance | Copy to replicas |
| Quorum | Consistency | Majority vote |
| Timeout | Failure detection | Kill unresponsive |

## Failure Types

| Type | Detection | Recovery |
|------|-----------|----------|
| Node crash | Heartbeat timeout | Restart/failover |
| Network partition | Lost messages | Wait/heal |
| Byzantine | Unexpected behavior | Abort/retry |

## Performance Tradeoffs

| Increase | Effect | Cost |
|----------|--------|------|
| Replicas | Availability | Write latency |
| Consistency level | Safety | Availability |
| Quorum size | Fault tolerance | Latency |

## Practical Tools

| Tool | Purpose | Type |
|------|---------|------|
| Consul | Service discovery | Go |
| etcd | Distributed config | Go |
| Zookeeper | Coordination | Java |
| Redis Sentinel | Redis HA | C |
| HAProxy | Load balancing | C |

## Key Terms

```
RPO: Recovery Point Objective (how much data loss OK)
RTO: Recovery Time Objective (downtime tolerance)
MTBF: Mean Time Between Failures
MTTR: Mean Time To Repair
CAP: Only 2 of 3 during partition
Quorum: > N/2 nodes
Leader: Single coordinator
```

## Quick Decision Guide

**Need strong consistency?**
→ CP system, accept availability loss

**Need high availability?**
→ AP system, accept eventual consistency

**Single data center?**
→ CA system possible

**Multi-region?**
→ CAP theorem forces AP or CP

**Small system?**
→ Master-slave simpler

**Large system?**
→ Quorum-based safer
