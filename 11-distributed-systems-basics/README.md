# 11 - Distributed Systems Basics

## What You'll Learn

- Distributed systems fundamentals
- CAP theorem (Consistency, Availability, Partition tolerance)
- Replication and partitioning
- Consensus algorithms
- Eventual consistency
- Service discovery
- Load balancing
- Fault tolerance

## Prerequisites

- Completed **10-scheduling-and-performance**
- Understanding of networking
- Basic client-server concepts

## Key Concepts

### 1. Distributed System Properties

**Spread Across Machines**: Data/compute split across nodes for horizontal scaling. Trade-off: simpler architectures but complex coordination. Network unreliable (packets lost, delayed, duplicated). Latency ~1ms within datacenter, ~100ms across regions.

**Key Challenges**: Partial failures (some nodes down, others up), no shared clock (can't order events by time), asynchronous (messages can arrive out-of-order or not at all). System must maintain consistency across network despite these constraints. Can distinguish network failure from node crash (timeout required).

### 2. CAP Theorem

During network partition, choose 2 of 3:
- **Consistency (C)**: All nodes see same data. Cost: slower writes, requires coordination.
- **Availability (A)**: Always responds (may return stale). Cost: inconsistent data.
- **Partition Tolerance (P)**: Continues during network split. Necessary to choose from C or A.

**Practical Trade-off**: Choose **CP** (unavailable but correct, like banks) or **AP** (available but inconsistent, like caches/social media). Most systems pick AP for responsiveness. Banks pick CP to prevent wrong balances.

### 3. Replication

**Master-Slave (Primary-Replica)**: One master accepts writes, slaves are read-only replicas. Simple, strong consistency. Lag: slaves see writes after async replication (milliseconds to seconds). Bottleneck: single write point. Use for: strong consistency needed, low write volume.

**Master-Master (Peer-to-Peer)**: Multiple nodes accept writes, replicate to each other. Higher write availability. Problem: conflicts (both write same key simultaneously). Resolution: last-write-wins (may lose data), app-defined logic, or abort. Use for: write availability critical.

**Quorum**: Need N/2+1 nodes agree (e.g., 3 nodes = 2 agree). Tolerates minority failures. For N=5, tolerate 2 failures. Slower (multi-node voting) but guarantees reading latest write.

### 4. Partitioning (Sharding)

**Why**: Single machine has storage/memory/CPU limits. Sharding splits data: each shard handles subset. Horizontal scaling: add machines, add shards.

**Strategies**: 
- **Range-based** (ID 1-1M shard 1, 1M-2M shard 2): Easy but hot spots (popular range overloads).
- **Hash-based** (hash(key) % shards): Uniform distribution, avoids hot spots. Problem: range queries scattered across shards.
- **Directory-based** (lookup table): Flexible, easy rebalancing, but adds lookup cost and SPOF.

**Rebalancing**: Adding/removing shards requires moving data (slow, complex). Online rebalancing needed for zero downtime.

### 5. Consensus

**Problem**: Nodes must agree despite failures/network delays. Requires safety (no wrong decisions) and liveness (eventually decides).

**Raft** (modern choice): Elect leader, leader makes decisions, followers replicate log. If leader fails, new election. Simple, used by etcd, Consul, most DBs.

**Paxos**: Original algorithm, powerful but complex (prepare → accept → learn phases). Used by Google Chubby, Zookeeper variant.

**Two-Phase Commit (2PC)**: Coordinator asks nodes "commit?", all vote yes/no, if all yes → commit. Problem: blocks waiting, coordinator failure = uncertainty. Use for strong atomicity only.

**Practical**: Raft in most systems. Quorum voting (N/2+1). Heartbeats detect failures. Election timeout triggers new leader. Tolerate up to N/2 failures with N nodes.

## Hands-on Lab: Service Discovery and Health Checks

### Lab Overview
Simulate distributed service discovery and health monitoring.

### Lab Commands

```bash
# 1. Simulate service with PID file
sleep 1000 & echo $! > service.pid

# 2. Check if service running
if [ -f service.pid ] && kill -0 $(cat service.pid); then
  echo "Service up"
fi

# Expected: Service up

# 3. Health check (simulate endpoint)
# Create mock: echo "healthy" > health.txt
cat health.txt

# Expected: healthy

# 4. Service registry (JSON file)
echo '{"services": [{"name": "web", "ip": "10.0.0.1", "port": 8080}]}' > services.json
cat services.json

# Expected: (JSON output)

# 5. Load balancer round-robin
ips=("10.0.0.1" "10.0.0.2" "10.0.0.3")
for ((i=0; i<5; i++)); do
  echo "Route to: ${ips[$((i % 3))]}"
done

# Expected: (rotating through IPs)

# 6. Simulate node failure
kill $(cat service.pid)

# 7. Detect failure
if ! kill -0 $(cat service.pid) 2>/dev/null; then
  echo "Node down, triggering failover"
fi

# Expected: Node down, triggering failover

# 8. Check cluster state
ps aux | grep sleep

# Expected: (list of running services)

# 9. Monitor heartbeat
while true; do
  date +%s
  sleep 2
done

# (Simulates heartbeat messages)

# 10. Log replication
echo "Message 1" >> log.txt
echo "Message 2" >> log.txt
cat log.txt

# Expected: (all messages replicated)
```

## Validation

```bash
# Can you simulate service discovery?
cat services.json && echo "✓ Discovery works"

# Understand replication?
echo "✓ Replication understood"

# Know CAP theorem?
echo "✓ CAP theorem understood"

# Handle failures?
echo "✓ Failure handling known"
```

## Cleanup

```bash
# Remove test files
rm -f service.pid services.json health.txt log.txt
pkill -f "sleep 1000" 2>/dev/null || true
```

## Common Mistakes

1. **CAP confusion**: Can't have all 3 during partition
2. **Replication overhead**: Slower writes for consistency
3. **Quorum size**: N/2+1, not all nodes
4. **Network partition**: Different from node failure
5. **Eventual consistency**: Data temporarily inconsistent

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Consensus stuck | Check: network partition, quorum |
| Data mismatch | Trigger: replication sync |
| Node unresponsive | Check: heartbeat, timeout |
| Partition detected | Wait for: healing or failover |
| Service discovery stale | Update: registry TTL |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for concepts
4. Move to **12-databases-for-devops** after completion

## Additional Resources

- CAP: Google "CAP Theorem"
- Raft: raft.github.io
- Consensus: "Designing Data-Intensive Applications"

