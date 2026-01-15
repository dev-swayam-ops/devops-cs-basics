# 11 - Distributed Systems Basics Solutions

## Easy Solutions (1-5)

### Solution 1: CAP Theorem

**Explanation**:
- Consistency: all nodes see same data
- Availability: always responsive
- Partition tolerance: works with split network

**Tradeoff**:
- CA: single machine (no network)
- CP: consistent, unavailable during partition (DB)
- AP: available, inconsistent (web cache)

**Examples**:
- CP: PostgreSQL, strong consistency
- AP: DNS, eventual consistency
- CA: impossible in distributed

---

### Solution 2: Service Registry

**Script**:
```bash
echo '{"services": [
  {"name": "api", "ip": "10.0.0.1", "port": 8080},
  {"name": "db", "ip": "10.0.0.2", "port": 5432}
]}' > services.json
```

**Explanation**:
- Discovery: find service by name
- Needed: dynamic IPs, many services
- Maintainer: coordination service (Consul, etcd)
- Update frequency: seconds to minutes

---

### Solution 3: Check Node Health

**Script**:
```bash
sleep 1000 & PID=$!
echo $PID > node.pid

# Check alive
kill -0 $PID && echo "UP" || echo "DOWN"

# Kill it
kill $PID

# Check again
kill -0 $PID 2>/dev/null && echo "UP" || echo "DOWN"
```

**Detection**:
- Manual: kill -0 check
- Automatic: heartbeat/watchdog
- Timeout: if no heartbeat N seconds

---

### Solution 4: Understand Replication

**Script**:
```bash
echo "v1" > primary.txt
cp primary.txt replica1.txt
echo "v2" > primary.txt
cat replica1.txt  # Still v1
```

**Sync**:
- Manual copy: stale after change
- Auto sync: pull from primary periodically
- Sync duration: RPO (Recovery Point Objective)

---

### Solution 5: Load Balancer Round-Robin

**Script**:
```bash
servers=("srv1" "srv2" "srv3")
for i in {0..4}; do 
  echo "Request $((i+1)): ${servers[$((i % 3))]}"
done
```

**Output**:
```
Request 1: srv1
Request 2: srv2
Request 3: srv3
Request 4: srv1
Request 5: srv2
```

**Benefit**: Fair distribution, no overload

---

## Medium Solutions (6-10)

### Solution 6: Network Partition

**Scenario**:
```
Network: [A, B] | [C, D, E]
Left: 2 nodes, right: 3 nodes
Quorum: 3 (majority of 5)
Left: can't achieve quorum → unavailable
Right: has quorum → available
```

**Behavior**:
- Left partition: reject writes
- Right partition: accept writes
- After heal: conflict resolution

---

### Solution 7: Quorum Write

**Calculation**:
- 5 replicas: quorum = (5/2)+1 = 3
- 1 down: 4 available ≥ 3 → write OK
- 2 down: 3 available ≥ 3 → write OK
- 3 down: 2 available < 3 → write FAIL
- Tolerance: 2 failures with 5

**Formula**: tolerate ⌊(N-1)/2⌋ failures

---

### Solution 8: Leader Election

**Process**:
1. Heartbeat: A → B, C
2. A fails: no heartbeat timeout
3. Election: B and C run algorithm
4. Highest ID or priority wins
5. Winner: becomes new leader

---

### Solution 9: Eventual Consistency

**Timeline**:
```
T0: Write "X=5" to master
T0: Master response (done)
T1: Async copy to replica1
T2: Async copy to replica2
T0-T1: Read replica might see "X=0" (stale)
T2+: All consistent
```

**Use cases**:
- Web caches: eventual OK
- Bank transfer: strong required
- Social media: eventual OK

---

### Solution 10: Distributed Logging

**Ordering**:
```
Logical clock:
Node A: msg1(clock=1), msg2(clock=2)
Node B: msg3(clock=1), msg4(clock=3)

Total order: msg1(1), msg3(1), msg2(2), msg4(3)
Conflict on clock=1: use node ID as tiebreaker
```

**Ensure**: consensus algorithm (Raft)

---

## Quick Concepts

```
CAP: Consistency/Availability/Partition (pick 2)
Replication: copies for fault tolerance
Consensus: agree on state
Quorum: majority vote (N/2+1)
Partition: network split
Leader: coordinator
Heartbeat: liveness check
```
