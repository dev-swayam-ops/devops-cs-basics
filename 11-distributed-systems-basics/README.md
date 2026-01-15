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

Distributed systems span multiple machines connected by a network. This introduces complexity but enables scalability and fault tolerance.

**Multiple Independent Machines**
- System not on single computer - spread across many nodes
- Each node is separate computer with own processor, memory, storage
- Enables horizontal scaling: add more machines for more capacity
- But adds complexity: coordination, communication overhead

**Network Communication**
- Only way machines communicate: sending messages over network
- Network is unreliable: packets can be lost, delayed, duplicated
- Messages take time to arrive: no instant communication
- Bandwidth limited: can't send unlimited data
- Latency: typical 1ms within datacenter, 100ms across continents

**Shared State**
- System maintains distributed state across multiple machines
- Changes at one node must eventually reflect at others
- Challenge: keeping state consistent across network
- Trade-off: consistency vs performance

**Partial Failures**
- Key difference from single-machine systems
- Some nodes can fail while others continue
- Example: node 1 crashed but nodes 2,3,4 still running
- System must detect failures and continue operating
- Cannot distinguish network failure from node crash

**No Shared Clock**
- Each machine has own clock (slightly different)
- No global synchronized time across machines
- Cannot use time to determine order of events
- Must use logical clocks or order numbers instead

**Asynchronous Communication**
- Messages can arrive in any order
- Cannot assume message arrives in bounded time
- Cannot assume messages arrive at all (lost)
- Makes guaranteeing order and reliability hard
- Applications must handle out-of-order and missing messages

### 2. CAP Theorem

The CAP theorem states that distributed systems can only guarantee two of three properties. Understanding this is critical for choosing architectures.

**Consistency (C)**
- All nodes see the same data at the same time
- If you read from any node, you get the latest value
- Write to one node, immediately visible everywhere
- Like a single machine - everyone sees same state
- Example: Bank account balance - all branches show same balance
- Cost: requires coordination, slower writes

**Availability (A)**
- System always responds to requests
- Every node can process requests (no blocking)
- No timeouts, no "please try again later"
- Highest possible uptime and responsiveness
- Example: cache that always returns (even if stale)
- Cost: might return outdated data

**Partition Tolerance (P)**
- System continues operating despite network splits
- Nodes split into groups that cannot communicate
- Partitioned groups can still operate independently
- Data may diverge between partitions
- System eventually heals when network reconnects
- Example: Some datacenters isolated due to network failure

**The Trade-off**
- You can have at most 2 of 3 during network partition
- CA: No partition tolerance - won't survive network split
- CP: Sacrifice availability - goes offline during partition
- AP: Sacrifice consistency - returns potentially stale data
- Most systems choose AP: availability more important than immediate consistency
- So choice is CP (correct but unavailable) or AP (available but inconsistent)
- Banks typically CP: unavailable rather than give wrong balance
- Social media typically AP: show old posts if network splits

### 3. Replication

Replication copies data across multiple nodes for redundancy and performance. Different strategies have different trade-offs.

**Master-Slave (Primary-Replica) Replication**
- One node is master/primary - accepts all writes
- Other nodes are slaves/replicas - read-only copies
- Master replicates writes to slaves asynchronously
- Slaves serve reads - distributes load
- Advantages: simple, strong consistency, clear master-of-truth
- Disadvantages: slaves lag behind master (replication delay), single write point (bottleneck)
- Use when: strong consistency important, write volume low

**Master-Master (Peer-to-Peer) Replication**
- Multiple nodes can accept writes
- Each node replicates writes to others
- No single master - democratic
- Advantages: higher write availability, no single point of failure
- Disadvantages: conflict resolution hard (both masters write same key), complex
- Conflicts: both masters write different values to same key simultaneously
- Resolution: last-write-wins (may lose data), application-defined (custom logic), abort (reject conflict)
- Use when: write availability critical, conflicts rare

**Quorum Replication**
- Voting system - need majority agreement
- To write: contact N/2+1 nodes, if all agree, apply write
- To read: contact N/2+1 nodes, get latest value
- Ensures consistency: quorum overlap guarantees seeing latest write
- Advantages: tolerates minority failures, eventual consistency
- Disadvantages: slower (must contact multiple nodes), complex
- N=3: need 2 nodes agree (tolerate 1 failure)
- N=5: need 3 nodes agree (tolerate 2 failures)

**Replication Lag**
- Time between write to master and visibility on slave
- Async replication: milliseconds to seconds delay
- During lag: slaves return stale data
- Problems: user writes data, reads it from slave, sees old version
- Solutions: read-after-write consistency, session consistency

### 4. Partitioning (Sharding)

Partitioning splits data across multiple nodes so each stores subset. Essential for scaling beyond single machine capacity.

**Why Partition**
- Single machine has limits: storage, memory, processing
- Partitioning: each machine handles subset of data
- Horizontal scaling: add more machines, add more shards
- Example: 1TB data across 10 machines = 100GB per machine

**Range-Based Partitioning**
- Shard by value range
- Example: IDs 1-1M on shard 1, 1M-2M on shard 2, etc.
- Advantages: easy to implement, range queries efficient
- Disadvantages: hot spots (popular range gets overloaded), uneven distribution
- Use when: data naturally ranges, even distribution

**Hash-Based Partitioning**
- Hash key, result determines shard
- Example: hash(user_id) % 10 determines which of 10 shards
- Advantages: uniform distribution, avoids hot spots
- Disadvantages: range queries scattered, rebalancing complex
- Use when: need even distribution, keys are lookups

**Directory-Based Partitioning**
- Lookup table: maps key to shard
- Example: directory maps user_id ranges to server IP
- Advantages: flexible, can rebalance easily, hot spot aware
- Disadvantages: directory single point of failure, extra lookup cost

**Rebalancing**
- When adding/removing shards, data must move
- Complex process: online rebalancing without downtime
- Time-consuming: copying terabytes of data
- Requires careful coordination to maintain consistency

### 5. Consensus

Consensus is the problem of making all nodes agree on a value despite failures and network issues. Many important algorithms solve this.

**The Problem**
- Distributed system must agree on decisions
- Some nodes may fail or be slow
- Network messages may be lost or delayed
- How to reach agreement despite all these problems?
- Must be safe (no wrong decisions) and live (eventually decide)

**Raft Algorithm**
- Simpler than Paxos, easier to understand
- Elects leader, leader makes decisions
- Followers replicate leader's log
- If leader fails, new leader elected
- Used by: etcd, Consul, many distributed databases
- Process: election → log replication → safety checks

**Paxos Algorithm**
- Original consensus algorithm (by Lamport)
- Very powerful but complex to understand and implement
- Used by: Google Chubby, Apache Zookeeper (uses variant)
- Phases: prepare phase, accept phase, learn phase
- More complex than Raft but handles more edge cases

**Two-Phase Commit (2PC)**
- Coordinator asks all nodes: "Can you commit?"
- Nodes vote yes/no (prepare phase)
- If all yes: coordinator tells all to commit (commit phase)
- If any no: coordinator tells all to abort
- Guarantees: all or nothing (atomic across nodes)
- Problem: blocks waiting for responses, coordinator failure causes uncertainty
- Use when: strong atomicity required, small number of participants

**Practical Consensus**
- Raft in most modern systems (simpler, good enough)
- Quorum voting for read consistency
- Heartbeats to detect failures
- Election timeouts to select new leader if leader dies
- Log replication to propagate decisions

**Failure Handling in Consensus**
- Node crash: detectable via heartbeat timeout, election process
- Network partition: partitioned nodes form quorum if they have it, elect new leader
- Byzantine failures: lying or arbitrary behavior (rare, use different algorithms)
- Majority approach: tolerate up to N/2 failures with N nodes

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

