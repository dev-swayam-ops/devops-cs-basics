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
```
Cache-aside: App checks cache first
Write-through: Write to cache and DB
Write-behind: Write to cache first, async to DB
TTL: Time-to-live (expiration)
```

### 2. Cache Patterns
- **Distributed cache**: Shared across servers
- **Local cache**: Per-server memory
- **LRU**: Least recently used eviction
- **TTL**: Automatic expiration

### 3. Message Queues
```
Producer: Sends message
Queue: Buffers message
Consumer: Processes message
Async: Producer doesn't wait
```

### 4. Pub/Sub Model
- Publisher: Sends message to topic
- Subscriber: Listens to topic
- Topic: Message group
- Broadcasting: All subscribers get message

### 5. Queue Types
- **FIFO**: First-in, first-out (reliable)
- **Priority**: High priority first
- **Dead letter**: Failed messages

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

