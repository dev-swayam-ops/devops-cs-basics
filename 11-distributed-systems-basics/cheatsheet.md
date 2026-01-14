# 11 - Distributed Systems Basics: Cheatsheet

## CAP Theorem

```
Choose 2 of 3:
- Consistency: All nodes see same data
- Availability: System always responds
- Partition Tolerance: Works despite network failures

Examples:
- CP: Traditional SQL databases (strong consistency)
- AP: NoSQL, eventual consistency (high availability)
- CA: Not possible in distributed systems
```

## Replication Strategies

| Type | Pros | Cons |
|------|------|------|
| **Master-Slave** | Simple, fast | Failover complexity |
| **Multi-Master** | High availability | Conflict resolution hard |
| **Quorum** | Strong consistency | Network overhead |

## Consensus Algorithms

```
Paxos:
- Complex but proven
- Used by Google Chubby

Raft:
- Simpler than Paxos
- Used by etcd, Consul

Byzantine Fault Tolerance:
- Handles malicious nodes
- High overhead
```

## Distributed Tracing

```bash
jaeger-query              # View traces
distributed-tracing       # Track requests across services
correlation-id           # Link related logs
span                      # Single operation timing
```

## Key Patterns

```
Load Balancing: Distribute load across servers
Caching: Reduce database queries
Sharding: Partition data by key
Circuit Breaker: Fail gracefully
Retry with Backoff: Handle transient failures
```

---
