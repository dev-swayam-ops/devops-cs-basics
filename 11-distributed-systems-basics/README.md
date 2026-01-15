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
```
Multiple machines
Network communication
Shared state
Partial failures (some nodes down)
No shared clock
Asynchronous
```

### 2. CAP Theorem
- **Consistency**: All nodes see same data
- **Availability**: Always responsive
- **Partition tolerance**: Works despite network split
- Pick 2 of 3 in network partition

### 3. Replication
- **Master-slave**: One writes, copies to slaves
- **Master-master**: Multiple write (conflict risk)
- **Quorum**: Majority vote (N/2 + 1)

### 4. Partitioning
- Data split across nodes
- Reduce per-node load
- Range-based or hash-based

### 5. Consensus
- **Raft**: Replicated state machine
- **Paxos**: Complex, powerful
- **Two-phase commit**: All or nothing

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

