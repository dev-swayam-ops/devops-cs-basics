# 13 - Caching and Queues Solutions

## Easy Solutions (1-5)

### Solution 1: Cache Benefits

**Speed Comparison**:
```
Memory (Redis): ~1 microsecond
Database (disk): ~1 millisecond
Difference: 1000x faster
```

**Trade-off**:
- Pro: Dramatically faster
- Con: Limited memory, stale data
- Use: High-volume, read-heavy workloads

---

### Solution 2: Set Cache Keys

**Commands**:
```bash
redis-cli SET user:1:name "Alice"
redis-cli SET user:2:email "bob@example.com"
redis-cli SET product:100:price "29.99"
```

**Key format**:
- Pattern: `entity:id:attribute`
- Readable and searchable
- Namespace-like structure

---

### Solution 3: Expire Cache Data

**Commands**:
```bash
redis-cli SET session:123 "active" EX 3600
redis-cli TTL session:123
# Returns: 3599 (countdown)

redis-cli PEXPIRE key 1000  # milliseconds
redis-cli EXPIREAT key 1234567890  # timestamp
```

**TTL States**:
- Positive: seconds until expiry
- -1: no expiration set
- -2: key doesn't exist

---

### Solution 4: Queue Operations

**FIFO Queue**:
```bash
redis-cli LPUSH queue "task1"
redis-cli LPUSH queue "task2"
redis-cli RPOP queue
# Returns: task1 (FIFO order)
```

**Operations**:
- LPUSH: add left (head)
- RPOP: remove right (tail)
- Result: FIFO behavior

---

### Solution 5: Cache Hit Rate

**Calculation**:
```
Hits: 90
Misses: 10
Hit rate: 90/(90+10) = 90%
```

**Interpretation**:
- 90%+: excellent
- 70-90%: good
- <70%: poor (reconsider TTL/keys)

---

## Medium Solutions (6-10)

### Solution 6: LRU Eviction

**Configuration**:
```bash
redis-cli CONFIG SET maxmemory 100mb
redis-cli CONFIG SET maxmemory-policy allkeys-lru
```

**Policies**:
- allkeys-lru: remove any least-used
- volatile-lru: remove expiring only
- allkeys-lfu: least frequently used
- random: no pattern

**Choice**:
- Session cache: volatile-lru (only sessions)
- General cache: allkeys-lru (most flexible)

---

### Solution 7: Pub/Sub Messaging

**Setup**:
```bash
# Terminal 1
redis-cli SUBSCRIBE alerts

# Terminal 2
redis-cli PUBLISH alerts "Message"

# Terminal 1 receives: "Message"
```

**Broadcasting**:
- Multiple subscribers: all receive
- Fire-and-forget: no persistence
- Subscribers must be listening

---

### Solution 8: Queue Consumer

**Pattern**:
```bash
# Producer
redis-cli LPUSH jobs '{"task": "email"}'

# Consumer (blocking)
redis-cli BRPOP jobs 0
# Waits for job, processes, deletes
```

**Reliability**:
- No persistence by default
- Use: RPOPLPUSH for atomic move
- Dead letter queue for failures

---

### Solution 9: Cache Invalidation

**Strategies**:
```
TTL-based: expires after N seconds
Event-based: pub/sub on update
Active: manually DELETE key
```

**Safest**:
- Short TTL + event invalidation
- Ensures freshness
- Tolerates occasional staleness

---

### Solution 10: Performance Impact

**Typical Results**:
```
Without cache: 100ms per request × 100 = 10s
With cache: 1ms per request × 100 = 100ms
Speed-up: 100x
```

**Worth it?**
- Yes if: read-heavy, high volume
- No if: write-heavy, low volume
- Storage cost: <1% of time saved

---

## Quick Commands

```bash
# Set and get
SET key value; GET key

# Expiration
SET key value EX 60
TTL key; PTTL key

# Queues
LPUSH queue item; RPOP queue
LLEN queue; LRANGE queue 0 -1

# Pub/Sub
PUBLISH topic message
SUBSCRIBE topic

# Cleanup
FLUSHALL; DEL key
```
