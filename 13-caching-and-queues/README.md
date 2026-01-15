# 13 - Caching and Queues

## What You'll Learn

- Caching strategies and patterns
- Redis basics and operations
- Cache invalidation techniques
- Message queues and pub/sub
- Queue-based architecture
- Cache performance optimization
- Queue monitoring and troubleshooting

## Prerequisites

- Completed **12-databases-for-devops**
- Understanding of databases
- Basic networking knowledge

## Key Concepts

### 1. Caching Strategies

Caching reduces latency and database load by keeping frequently-accessed data in fast storage. Different strategies have different consistency guarantees.

**Cache-Aside (Lazy Loading)**
- Application code checks cache first
- If cache hit: return cached value immediately
- If cache miss: fetch from database, store in cache, return
- Advantages: simple, cache only stores accessed data, stale data acceptable
- Disadvantages: first access slow (cache miss), stale data briefly
- Best for: read-heavy workloads, tolerate eventual consistency
- Implementation: `if value in cache: return cache[key]; else: value = db[key]; cache[key] = value; return value`

**Write-Through**
- Write goes to both cache AND database simultaneously
- Waits for both to complete before returning
- Advantages: cache always up-to-date, no stale data
- Disadvantages: slow (must wait for database), both fail if either fails
- Best for: strong consistency required
- Implementation: `cache[key] = value; db[key] = value; return success`

**Write-Behind (Write-Back)**
- Write goes to cache first (fast)
- Database updated asynchronously later
- Returns immediately after cache write
- Advantages: very fast writes, decouples cache and database
- Disadvantages: data loss risk (crash before database write), complex
- Best for: high write throughput, data loss acceptable, durable queue
- Implementation: `cache[key] = value; queue_async(update_db, key, value); return success`

**TTL (Time To Live)**
- Cached values automatically expire after time period
- Example: cache entry expires after 300 seconds
- Advantages: old data eventually removed, simple expiration
- Disadvantages: stale data up to TTL duration
- Short TTL: fresh data but more cache misses
- Long TTL: fewer misses but stale data longer
- Balance based on data freshness requirements

### 2. Cache Patterns

Different cache architectures serve different purposes in distributed systems.

**Distributed Cache**
- Shared cache accessible from multiple servers
- Central cache cluster: all servers access same data
- Examples: Redis cluster, Memcached cluster
- Advantages: single source of truth, consistent across all servers
- Disadvantages: network latency, single point of failure (without replication)
- Use when: multiple servers need same cache, consistency important
- Architecture: load balancer → multiple app servers → single cache cluster

**Local Cache (In-Process Cache)**
- Each server maintains own cache in its memory
- Not shared across servers
- Examples: Java HashMap, Python dict, Guava Cache
- Advantages: fastest (in-memory, no network), no single point of failure
- Disadvantages: inconsistent between servers, duplicated data, memory waste
- Use when: single server, or acceptable data inconsistency
- Problem: server A has value X, server B has value Y (not same)

**LRU Eviction (Least Recently Used)**
- When cache full, remove least recently accessed item
- "Least recently used" = hasn't been accessed for longest time
- Advantages: automatically keeps hot data, removes cold data
- Disadvantages: LRU tracking has overhead
- Alternative: LFU (least frequently used), FIFO (oldest first)
- Example: 100MB cache, 500MB data requested → LRU keeps 100MB of most-used

**TTL Expiration**
- Entries automatically removed after time
- Periodic cleanup: scan for expired entries
- Lazy cleanup: remove on access if expired
- Advantages: automatic cleanup, old data removed
- Disadvantages: stale data exists until expiration
- Trade-off: shorter TTL = fresher data but more misses

### 3. Message Queues

Queues decouple components in distributed systems, allowing asynchronous processing and load buffering.

**Basic Architecture**

**Producer**
- Component that sends messages
- Doesn't need to wait for processing
- Can continue immediately after queueing
- Example: web request handler that queues email

**Queue**
- Buffers messages between producer and consumer
- FIFO ordering: first message in, first out
- Durable: messages persisted (survive crashes)
- Addresses: distributed consumers

**Consumer**
- Component that processes messages
- Pulls from queue, processes, acknowledges
- Multiple consumers can process in parallel
- Example: worker processes that send emails

**Benefits of Async**
- Producer not blocked: send message and continue
- Decoupling: producer doesn't know about consumer
- Buffering: handle spikes (queue fills, consumers catch up)
- Scaling: add more consumers to process faster
- Resilience: producer continues if consumer down

**Message Acknowledgment**
- Consumer processes message
- If successful: acknowledges message (removed from queue)
- If unsuccessful: nack (negative ack) message (stays in queue)
- Prevents loss: message only removed if definitely processed
- Retry: failed messages reprocessed

### 4. Pub/Sub Model

Publish-Subscribe is a messaging pattern where multiple consumers receive same message.

**Components**

**Publisher**
- Sends message to topic
- Doesn't know who will receive it
- Decoupled from subscribers
- Example: events service publishes user.created event

**Subscriber**
- Listens to specific topic
- Receives all messages on that topic
- Can subscribe/unsubscribe dynamically
- Example: email service subscribes to user.created, sends welcome email

**Topic**
- Named message group
- Examples: user.created, order.placed, payment.completed
- Publishers publish to topic, all subscribers get message
- Multiple subscribers to same topic all receive message

**Broadcasting**
- Single message delivered to all current subscribers
- Example: broadcast user.deleted → payment service, analytics service, etc. all notified
- Advantages: decopled, easy to add new subscribers
- Disadvantages: ordering (subscribers process at different rates), no ordering guarantee

**Fan-Out Pattern**
- One message triggers multiple independent processes
- Example: order.placed triggers: payment processing, inventory, notification, analytics
- Parallel execution: all happen simultaneously
- Independence: failure of one doesn't stop others (if using durable queue)

### 5. Queue Types and Dead Letters

Different queue types provide different ordering and processing guarantees.

**FIFO Queues**
- First-in, first-out order
- Messages processed in order received
- Advantages: ordered processing, predictable
- Disadvantages: slower (must maintain order), head-of-line blocking
- Use when: order critical (financial transactions, state machines)
- Example: account transaction queue

**Priority Queues**
- Messages have priority
- High priority processed first, regardless of arrival order
- Advantages: urgent work processed immediately
- Disadvantages: low priority messages can starve
- Use when: some messages more important
- Example: VIP customer support tickets processed before regular

**Dead Letter Queue (DLQ)**
- Messages that failed to process
- After N retries, moved to DLQ
- Prevents infinite loops on poisoned messages
- Advantages: don't lose messages, don't block queue
- Disadvantages: requires manual investigation and replay
- Monitoring: DLQ should trigger alerts
- Recovery: investigate, fix bug, replay from DLQ

**At-Least-Once Delivery**
- Message delivered at least once
- May be duplicated if consumer crashes
- Consumer must handle duplicates (idempotent)
- Advantages: no message loss
- Disadvantages: duplicate processing possible

**Exactly-Once Delivery**
- Message delivered exactly once
- No duplicates, no loss
- Very difficult to guarantee
- Usually: at-least-once + deduplication
- Trade-off: at-least-once simpler, need idempotent consumer

**Queue Monitoring**
- Queue depth: number of pending messages
- Consumer lag: how far behind consumers are
- Process rate: messages/second
- Error rate: % failing
- Alerts: if depth growing (consumers slow), if lag increasing

## Hands-on Lab: Redis Cache and Queue

### Lab Overview
Use Redis for caching and message queueing.

### Lab Commands

```bash
# 1. Start Redis
redis-server &

# 2. Connect to Redis
redis-cli

# 3. Set key-value
SET mykey "Hello"

# Expected: OK

# 4. Get value
GET mykey

# Expected: "Hello"

# 5. Set with expiration
SET cache_key "data" EX 60

# Expected: OK (expires in 60 seconds)

# 6. Check TTL
TTL cache_key

# Expected: 59 (or similar, countdown)

# 7. List all keys
KEYS *

# Expected: (all keys)

# 8. Push to queue (list)
LPUSH myqueue "job1"
LPUSH myqueue "job2"

# 9. Pop from queue
LPOP myqueue

# Expected: "job2" (LIFO order)

# 10. Queue length
LLEN myqueue

# Expected: 1
```

## Validation

```bash
# Can you connect to Redis?
redis-cli ping && echo "✓ Redis works"

# Set and get keys?
redis-cli SET test 1 && redis-cli GET test && echo "✓ Cache works"

# Use queues?
echo "✓ Queue operations understood"

# Know TTL?
echo "✓ Expiration understood"
```

## Cleanup

```bash
# Flush all data
redis-cli FLUSHALL

# Shutdown Redis
redis-cli SHUTDOWN
```

## Common Mistakes

1. **Cache stampede**: Many requests on cache miss
2. **Stale data**: TTL too long
3. **Cache eviction**: LRU removes needed data
4. **Queue blocking**: Consumer blocks producer
5. **Message loss**: No persistence setting

## Troubleshooting

| Problem | Solution |
|---------|----------|
| High cache miss rate | Check: TTL, key patterns |
| Memory full | Configure: maxmemory, eviction |
| Slow queue processing | Check: consumer speed, parallelism |
| Lost messages | Enable: persistence (RDB/AOF) |
| Network timeout | Check: connection pool, timeout |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **14-security-basics** after completion

## Additional Resources

- Redis: redis.io/commands
- Caching: "Cache Patterns" blog posts
- Queues: "Message Queue Patterns"

