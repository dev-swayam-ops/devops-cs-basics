# 11 - Distributed Systems Basics Exercises

## Easy Exercises (1-5)

### Exercise 1: Understand CAP Theorem
**Objective**: Know the tradeoff.

**Task**:
- CAP = Consistency, Availability, Partition tolerance
- In network partition, choose 2 of 3
- AP systems: available but inconsistent (base)
- CP systems: consistent but unavailable
- Which is database: CA/AP/CP?
- Example: what would you choose?

---

### Exercise 2: Simulate Service Registry
**Objective**: Store service locations.

**Task**:
```bash
echo '{"services": [{"name": "api", "ip": "10.0.0.1"}]}' > services.json
cat services.json
```
- What's service discovery?
- Why needed in distributed?
- Who maintains registry?
- How often updated?

---

### Exercise 3: Check Node Health
**Objective**: Monitor service status.

**Task**:
- Create: `sleep 1000 & echo $! > node.pid`
- Check: `kill -0 $(cat node.pid) && echo UP`
- Kill node: `kill $(cat node.pid)`
- Check again: still says UP?
- How to auto-detect failure?

---

### Exercise 4: Understand Replication
**Objective**: Know data copies.

**Task**:
- File = primary: `echo "data" > primary.txt`
- Replica 1: `cp primary.txt replica1.txt`
- Replica 2: `cp primary.txt replica2.txt`
- Modify primary: `echo "new" > primary.txt`
- Replicas still old?
- When do they sync?

---

### Exercise 5: Load Balancer Round-Robin
**Objective**: Distribute requests.

**Task**:
```bash
servers=("srv1" "srv2" "srv3")
for i in {1..5}; do echo ${servers[$((i % 3))]};done
```
- Which server gets request 1? 2? 3?
- Pattern: fair distribution?
- Better than random?

---

## Medium Exercises (6-10)

### Exercise 6: Simulate Partition
**Objective**: See split-brain.

**Task**:
- Network partition: nodes can't communicate
- Quorum: N/2+1 nodes
- 3 nodes: quorum = 2
- 1 node partition: can't write
- What happens to client requests?

---

### Exercise 7: Implement Quorum Write
**Objective**: Consensus write.

**Task**:
- Writes need: N/2+1 replicas
- 5 replicas: need 3 for quorum
- 1 down, still write? (4/5 ≥ 3?)
- 2 down, can still write? (3/5 ≥ 3?)
- Resilience: how many failures tolerate?

---

### Exercise 8: Leader Election
**Objective**: Choose coordinator.

**Task**:
- Nodes: A, B, C
- A is leader (heartbeat)
- A fails: B and C detect
- Who becomes leader?
- Tie-breaking: election algorithm needed?

---

### Exercise 9: Eventual Consistency
**Objective**: Understand async replication.

**Task**:
- Write to master: immediately
- Replicate to slaves: delayed
- Read from slave: stale data possible
- Use case: OK with stale?
- Strong consistency cost?

---

### Exercise 10: Distributed Logging
**Objective**: Ordered events.

**Task**:
- Log entry: "User A logged in"
- All nodes must see same order
- Ensure: with timestamps or sequence?
- Consensus log: all replicas same?

---

## Submission Tips

1. CAP theorem forces tradeoff (can't ignore)
2. Replication: write slower, read faster
3. Consensus: majority vote reliable
4. Network partition: worst case
5. Eventual consistency: practical compromise
