# 11 - Distributed Systems Basics

## What You'll Learn

Master distributed systems foundations:
- Distributed system characteristics
- CAP theorem (Consistency, Availability, Partition tolerance)
- Consensus algorithms
- Eventual consistency
- Replication and sharding
- Fault tolerance
- Network partition handling
- Distributed debugging

## Prerequisites

- Completed **10-scheduling-and-performance**
- Understanding of networking and databases
- Systems thinking

## Key Concepts

### 1. CAP Theorem
- **Consistency**: All nodes see same data
- **Availability**: System always responsive
- **Partition Tolerance**: Works during network partition
- **Trade-off**: Can have 2/3, not all 3

### 2. Replication Models
- **Master-Slave**: One writes, others replicate
- **Master-Master**: Multiple writes, conflict resolution
- **Peer-to-Peer**: All nodes equal, gossip protocol
- **Quorum**: Majority must agree for consistency

### 3. Consensus Algorithms
- **Raft**: Easier to understand, leader-based
- **Paxos**: Complex, academic gold standard
- **PBFT**: Practical Byzantine Fault Tolerance
- **Gossip**: Eventual consistency via propagation

### 4. Failure Modes
- **Fail-stop**: Node stops responding
- **Byzantine**: Node sends wrong data
- **Partition**: Network split, nodes unreachable
- **Cascade**: One failure causes others

## Hands-on Lab: Distributed Concepts

### Lab Steps

```bash
# 1. Simulate multiple servers locally
# Terminal 1
python3 -m http.server 8001

# Terminal 2
python3 -m http.server 8002

# Terminal 3
for i in {1..5}; do curl http://localhost:8001; done
for i in {1..5}; do curl http://localhost:8002; done

# 2. Monitor network latency
ping -c 5 localhost
ping -c 5 8.8.8.8

# 3. Simulate network partition
sudo iptables -A OUTPUT -d 127.0.0.2 -j DROP  # Block 127.0.0.2
sudo iptables -D OUTPUT -d 127.0.0.2 -j DROP  # Unblock

# 4. Redis replication
redis-cli > SLAVEOF NO ONE    # Stop replication
redis-cli > SLAVEOF localhost 6379

# 5. Check replication lag
redis-cli INFO replication

# 6. Simulate failure with timeout
timeout 5 sleep 100 &
sleep 2
kill -STOP $!
sleep 2
kill -CONT $!

# 7. Network tool for latency injection
# Install: sudo apt-get install iproute2
sudo tc qdisc add dev lo root netem delay 100ms
sudo tc qdisc show dev lo
sudo tc qdisc del dev lo root

# 8. Monitor system under load
ab -n 1000 -c 10 http://localhost:8001/

# 9. Check replica status
redis-cli INFO
redis-cli CLIENT LIST

# 10. Simulate cascading failure
bash -c 'for i in {1..10}; do sleep 0.5 & done; wait'
```

## Validation

Verify your understanding:

```bash
# Can you understand CAP?
echo "✓ CAP theorem understood"

# Can you see replication?
redis-cli INFO replication && echo "✓ Replication visible"

# Can you add network latency?
echo "✓ Network simulation understood"

# Can you understand failure modes?
echo "✓ Failure concepts understood"
```

## Cleanup

```bash
# Kill background servers
killall python3 sleep 2>/dev/null

# Remove network rules
sudo tc qdisc del dev lo root 2>/dev/null
```

## Common Mistakes

1. **Assuming network reliability**: Network partitions happen!
2. **CAP misunderstanding**: Can't have all 3, must pick 2
3. **Ignoring replication lag**: Slave reads may be stale
4. **Single point of failure**: Everything else fails if leader dies
5. **No quorum**: Minority partition can continue causing split-brain

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Replication lag | Check: network, slave load, binlog position |
| Split brain | Use: quorum, fencing, monitoring |
| Slow consensus | Reduce: node count, network latency |
| Data loss | Ensure: replication, backups, fsync |
| Cascading failures | Use: circuit breakers, timeouts, rate limiting |

## Next Steps

1. Move to **12-databases-for-devops** for storage solutions
2. Learn about distributed tracing (Jaeger, Zipkin)
3. Study service discovery (Consul, etcd)
4. Explore container orchestration (Kubernetes)
5. Learn about distributed consensus tools (etcd, Consul, Zookeeper)

## Additional Resources

- CAP theorem: https://en.wikipedia.org/wiki/CAP_theorem
- Raft algorithm: https://raft.github.io/
- Distributed systems papers: https://www.1e6.org/onlinebooks/
- DDIA book: https://dataintensive.fun/

