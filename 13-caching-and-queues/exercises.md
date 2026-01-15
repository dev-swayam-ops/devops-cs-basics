# 13 - Caching and Queues Exercises

## Easy Exercises (1-5)

### Exercise 1: Understand Cache Benefits
**Objective**: Know why cache matters.

**Task**:
- Without cache: Every request hits database
- With cache: Frequent requests hit memory (fast)
- Which is faster: Redis or database?
- Trade-off: memory vs accuracy
- Stale data OK? (depends on use case)

---

### Exercise 2: Set Cache Keys
**Objective**: Basic Redis operations.

**Task**:
```bash
redis-cli SET user:1:name "Alice"
redis-cli GET user:1:name
```
- Key format: `prefix:id:field`
- Value: string data
- Can you set multiple keys?
- Keys persist after logout?

---

### Exercise 3: Expire Cache Data
**Objective**: Automatic invalidation.

**Task**:
```bash
redis-cli SET session:123 "active" EX 3600
redis-cli TTL session:123
```
- TTL: seconds until expiration
- Negative TTL: what happens?
- No TTL: data persists forever?
- Use case: sessions (short TTL)?

---

### Exercise 4: Queue Operations
**Objective**: FIFO queue basics.

**Task**:
```bash
redis-cli LPUSH tasks "task1"
redis-cli LPUSH tasks "task2"
redis-cli RPOP tasks
```
- Push: add to queue
- Pop: remove from queue
- Order: FIFO or LIFO?
- Empty queue: what happens?

---

### Exercise 5: Cache Hit Rate
**Objective**: Measure efficiency.

**Task**:
- Hit: key found in cache
- Miss: key not found (hit database)
- High hit rate: good?
- Low hit rate: why?
- Monitor: KEYS count vs requests

---

## Medium Exercises (6-10)

### Exercise 6: LRU Eviction Policy
**Objective**: Manage memory limits.

**Task**:
- maxmemory: cache size limit
- eviction: what to delete when full?
- LRU: remove least recently used
- LFU: remove least frequently used
- Which better for your use case?

---

### Exercise 7: Pub/Sub Messaging
**Objective**: Broadcasting pattern.

**Task**:
```bash
# Terminal 1: Subscribe
redis-cli SUBSCRIBE alerts

# Terminal 2: Publish
redis-cli PUBLISH alerts "Server down!"
```
- Publisher: doesn't wait
- Subscriber: receives immediately
- Multiple subscribers: all get message?

---

### Exercise 8: Queue Consumer Pattern
**Objective**: Job processing.

**Task**:
```bash
# Producer: LPUSH queue "job1"
# Consumer: RPOP queue (processes)
# Can multiple consumers share?
# What if consumer crashes?
```
- Blocking pop: wait for jobs
- Non-blocking: return immediately
- Dead letter: failed jobs?

---

### Exercise 9: Cache Invalidation
**Objective**: Keep cache fresh.

**Task**:
- Update database: invalidate cache
- TTL-based: automatic expiration
- Event-based: publish invalidation
- Which is safest?
- Stale data risk?

---

### Exercise 10: Performance Comparison
**Objective**: Cache impact measurement.

**Task**:
- Time 100 requests: with cache
- Time 100 requests: without cache
- Difference: 10x? 100x faster?
- Memory trade-off: worth it?

---

## Submission Tips

1. Use KEYS pattern for exploration
2. TTL negative means already expired
3. LPUSH/RPOP for FIFO (reverse order)
4. Expiration automatic (no manual delete)
5. Pub/Sub: subscribers must be listening
