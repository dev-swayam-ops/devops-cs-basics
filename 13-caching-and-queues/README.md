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

**Cache-Aside (Lazy)**: App checks cache. Miss? Fetch from DB, cache it, return. Pro: simple, only caches accessed data. Con: first access slow (miss), brief stale data. Best for: read-heavy, eventual consistency acceptable.

**Write-Through**: Write to cache AND DB simultaneously. Pro: always consistent. Con: slow (wait for DB). Best for: strong consistency required.

**Write-Behind (Write-Back)**: Write to cache (fast), async update DB. Pro: very fast writes. Con: data loss risk (crash before DB write). Best for: high throughput, durable queue, data loss acceptable.

**TTL (Time To Live)**: Auto-expire entries. Short TTL = fresh but more misses. Long TTL = fewer misses but stale. Balance based on freshness requirements.

### 2. Cache Patterns

**Distributed Cache** (Redis, Memcached): Shared across servers. Single source of truth, consistent. Con: network latency, SPOF without replication. Use when: multi-server needs same cache, consistency critical.

**Local Cache** (in-process): Each server's own memory. Fastest (no network). Con: inconsistent between servers (server A has X, server B has Y), memory duplication. Use when: single server or acceptable inconsistency.

**LRU Eviction**: When full, remove least-recently-used. Keeps hot data, removes cold. Alternative: LFU (frequency), FIFO (oldest). Tracking overhead.

**TTL Expiration**: Auto-remove after time. Periodic scan or lazy (check on access). Trade: short TTL = fresh, long TTL = fewer misses.

### 3. Message Queues

**Components**: Producer (sends, doesn't wait), Queue (buffers messages, FIFO, durable), Consumer (pulls, processes, acknowledges).

**Benefits**: Producer not blocked. Decoupled (producer ≠ consumer). Buffering handles spikes. Parallel consumers scale. Resilience (producer continues if consumer down).

**Acknowledgment**: Consumer processes, acknowledges (removed). Failed: nack (stays in queue), retry. Prevents loss—message only removed if definitely processed.

**At-Least-Once vs Exactly-Once**: At-least-once = may duplicate, need idempotent consumer. Exactly-once = hard, usually at-least-once + dedup.

### 4. Pub/Sub Model

**Components**: Publisher (sends to topic, doesn't know subscribers), Subscriber (listens to topic, receives all messages), Topic (named message group: user.created, order.placed, etc.).

**Broadcasting**: Single message delivered to all current subscribers. Decoupled. Easy to add subscribers. Con: ordering (subscribers process at different rates), no guarantee on delivery order.

**Fan-Out Pattern**: One message triggers multiple independent processes (payment, inventory, notification, analytics all run in parallel). Failure of one doesn't stop others (if durable queue).

### 5. Queue Types and Dead Letters

**FIFO Queue**: Strict ordering, messages processed in order. Use for: sequential tasks (transactions, state machines). Trade-off: slower than priority queue.

**Priority Queue**: High priority first, regardless of arrival. Use for: urgent work (alerts, VIP requests). Risk: low priority starvation.

**Dead Letter Queue (DLQ)**: Holds messages that failed N retries. Prevents poisoned messages from blocking queue. Action: investigate failure → fix bug → replay from DLQ.

**Monitoring**: Queue depth (pending msgs), consumer lag (behind by how many?), process rate, error rate. Alert on depth increase (slow consumers) or DLQ growth (failures).

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

