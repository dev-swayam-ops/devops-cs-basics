# 13 - Caching and Queues Cheatsheet

## Redis Connection Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `redis-cli` | Connect to Redis | Default localhost:6379 |
| `redis-cli -h host -p port` | Remote connection | Custom server |
| `redis-cli PING` | Test connection | Returns PONG |
| `redis-cli SHUTDOWN` | Stop server | Graceful shutdown |
| `redis-server` | Start server | Foreground or background |

## Key-Value Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `SET key value` | Store string | `SET name "Alice"` |
| `GET key` | Retrieve value | Returns value or nil |
| `DEL key` | Delete key | Removes from cache |
| `EXISTS key` | Check if exists | Returns 1 or 0 |
| `INCR key` | Increment number | Atomic +1 |
| `DECR key` | Decrement number | Atomic -1 |
| `APPEND key val` | Append string | Concatenate |
| `STRLEN key` | String length | Returns length |

## Expiration Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `EXPIRE key seconds` | Set TTL | `EXPIRE key 60` |
| `TTL key` | Time remaining | Seconds left |
| `PTTL key` | TTL milliseconds | More precise |
| `PERSIST key` | Remove expiration | Keep forever |
| `EXPIREAT key timestamp` | Expire at time | Unix timestamp |
| `SET key value EX seconds` | Set with TTL | Combined operation |

## List/Queue Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `LPUSH list item` | Push left (head) | Add to front |
| `RPUSH list item` | Push right (tail) | Add to back |
| `LPOP list` | Pop left | Remove from front |
| `RPOP list` | Pop right | Remove from back |
| `LLEN list` | List length | Item count |
| `LRANGE list start stop` | View items | Range of items |
| `BRPOP list timeout` | Blocking pop | Wait for item |
| `RPOPLPUSH src dst` | Atomic move | Between lists |

## Pub/Sub Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `PUBLISH topic message` | Send message | Broadcast |
| `SUBSCRIBE topic` | Listen to topic | Receive messages |
| `UNSUBSCRIBE topic` | Stop listening | Disconnect |
| `PSUBSCRIBE pattern` | Pattern subscribe | Wildcard topics |
| `PUBSUB CHANNELS` | Active channels | Show topics |

## Database Management

| Command | Purpose | Example |
|---------|---------|---------|
| `FLUSHDB` | Clear current DB | Remove all keys |
| `FLUSHALL` | Clear all DBs | Dangerous! |
| `DBSIZE` | Key count | How many keys |
| `SELECT db` | Switch database | 0-15 typically |
| `SAVE` | Write to disk | Synchronous |
| `BGSAVE` | Background save | Async write |

## Key Management

| Command | Purpose | Example |
|---------|---------|---------|
| `KEYS pattern` | Find keys | Glob pattern |
| `SCAN cursor` | Iterate keys | Non-blocking |
| `TYPE key` | Get data type | string, list, set |
| `RANDOMKEY` | Random key | For testing |
| `RENAME key newkey` | Rename | Change key name |
| `RENAMENX key new` | Safe rename | Fail if exists |

## Server Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `INFO` | Server info | Stats and config |
| `CONFIG GET param` | Get setting | Check parameter |
| `CONFIG SET param val` | Change setting | Temporary change |
| `MONITOR` | Watch commands | Live command stream |
| `SLOWLOG GET` | Slow queries | Performance issues |

## Cache Patterns

| Pattern | Use Case | Implementation |
|---------|----------|-----------------|
| Cache-aside | General | Check cache, fallback to DB |
| Write-through | Consistency | Write to cache and DB |
| Write-behind | Performance | Async persist |
| Refresh-ahead | Predictable | Preload before expiry |

## Eviction Policies

| Policy | Behavior | Best for |
|--------|----------|----------|
| noeviction | No removal (error) | Strict limit |
| allkeys-lru | Remove any LRU | General cache |
| volatile-lru | Remove expiring LRU | Session store |
| allkeys-lfu | Remove any LFU | Frequency-based |
| random | Remove random | Simple |

## Performance Optimization

| Technique | Benefit | Cost |
|-----------|---------|------|
| Compression | Smaller memory | CPU overhead |
| Batching | Fewer roundtrips | Complexity |
| Pipelining | Parallel requests | Memory (buffering) |
| Persistence (RDB) | Durability | Disk writes |

## Monitoring Commands

```bash
# Memory usage
redis-cli INFO memory

# Connected clients
redis-cli INFO clients

# Operations per second
redis-cli INFO stats

# Keyspace stats
redis-cli INFO keyspace

# Monitor live
redis-cli MONITOR

# Slowlog
redis-cli SLOWLOG GET 10
```

## Configuration Tips

```bash
# Persistent storage
appendonly yes          # AOF (append-only file)
save 900 1             # RDB: save if 1 key changed in 900s

# Memory management
maxmemory 100mb
maxmemory-policy allkeys-lru

# Network
timeout 0              # No idle timeout
tcp-keepalive 300      # Connection health

# Replication
slaveof host port      # Slave configuration
```

## Quick Reference

```bash
# Basic cache operation
SET cache_key "value" EX 3600
GET cache_key

# Queue pattern
LPUSH queue job1
RPOP queue

# Pub/Sub
PUBLISH topic message
SUBSCRIBE topic

# Monitor
KEYS *
DBSIZE
INFO
```
