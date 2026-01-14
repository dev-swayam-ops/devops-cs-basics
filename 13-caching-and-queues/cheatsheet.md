# 13 - Caching and Queues: Cheatsheet

## Caching Strategies

| Strategy | When to Use | Example |
|----------|------------|---------|
| **Write-Through** | Need consistency | Banking |
| **Write-Behind** | High performance needed | Logging |
| **Lazy Load** | Unpredictable access | Web cache |
| **TTL-Based** | Frequently changing data | Sessions, config |

## Redis Commands

```bash
# String
SET key value
GET key
INCR counter

# List
LPUSH list value        # Add to left
RPOP list               # Remove from right
LLEN list               # List length

# Hash
HSET key field value
HGET key field
HGETALL key

# Set
SADD set member
SMEMBERS set

# Sorted Set
ZADD zset 1 member
ZRANGE zset 0 -1

# Expiration
EXPIRE key 3600         # Expire in 1 hour
TTL key                 # Check TTL
```

## Queue Patterns

```
FIFO Queue:
- In: Enqueue message
- Out: Dequeue first-in
- Use: Order processing

Priority Queue:
- In: Enqueue with priority
- Out: Highest priority first
- Use: Urgent tasks

Pub/Sub:
- Publish: Send to topic
- Subscribe: Receive from topic
- Use: Broadcast events
```

## Memcached vs Redis

```
Memcached:
- Simple key-value
- Fast, distributed
- No persistence

Redis:
- Data structures
- Persistence options
- Built-in replication
- Pub/Sub
```

## Cache Invalidation

```
Strategies:
1. TTL: Automatic expiry
2. Event-Based: Invalidate on change
3. Versioning: Change cache key
4. Scan & Invalidate: Clear matching keys

Challenges:
- Cache stampede: Too many requests on miss
- Solution: Lock or queuing
```

## Monitoring

```bash
INFO stats              # Cache stats
DBSIZE                  # Number of keys
MONITOR                 # Live command trace
CLIENT LIST             # Connected clients
```

---
