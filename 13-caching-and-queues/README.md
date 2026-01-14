# 13 - Caching and Queues

## What You'll Learn

Master caching and message queues:
- Caching strategies (LRU, LFU, TTL)
- Cache invalidation and coherence
- Message queue patterns
- Distributed queues (RabbitMQ, Kafka)
- Pub/Sub systems
- Performance bottleneck mitigation
- Monitoring queues and caches

## Prerequisites

- Completed **12-databases-for-devops**
- Understanding of databases and APIs
- Performance optimization mindset

## Key Concepts

### 1. Caching Strategies
- **LRU (Least Recently Used)**: Discard least recently accessed
- **LFU (Least Frequently Used)**: Discard least frequently accessed
- **TTL (Time-To-Live)**: Expire after time period
- **Write-Through**: Write to cache and DB
- **Write-Back**: Write to cache, flush to DB later

### 2. Cache Invalidation
- Hardest problem in computer science!
- **Time-based**: TTL expiration
- **Event-based**: Explicit invalidation
- **LRU/LFU**: Capacity-based eviction
- **Dependency-based**: Invalidate related entries

### 3. Message Queues
- Decouple producer from consumer
- Async processing
- Guaranteed delivery
- Ordering (some systems)
- Examples: RabbitMQ, Kafka, SQS

### 4. Queue Patterns
- **Work Queue**: Task distribution
- **Pub/Sub**: Publish to many subscribers
- **RPC Queue**: Request-reply pattern
- **Priority Queue**: High-priority tasks first

## Hands-on Lab: Caching and Queues

### Lab Steps

```bash
# 1. Redis caching
redis-cli > SET user:1:profile "{name:john}" EX 3600
redis-cli > GET user:1:profile
redis-cli > TTL user:1:profile

# 2. Cache invalidation
redis-cli > SET key1 "value1"
redis-cli > GET key1
redis-cli > DEL key1

# 3. RabbitMQ message queue
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management

# 4. Publish message
rabbitmqctl declare queue name=task_queue durable=true
rabbitmq-publish -x '' -r task_queue -p 'message body'

# 5. Consumer listening
rabbitmq-consume -q task_queue

# 6. Kafka for streaming
docker run -d --name kafka -p 9092:9092 confluentinc/cp-kafka

# 7. Create Kafka topic
kafka-topics --create --topic events --partitions 3 --replication-factor 1

# 8. Produce to topic
echo "event data" | kafka-console-producer --topic events --bootstrap-server localhost:9092

# 9. Consume from topic
kafka-console-consumer --topic events --bootstrap-server localhost:9092

# 10. Monitor queue depth
redis-cli > LLEN task_queue
rabbitmqctl list_queues
```

## Validation

Verify your understanding:

```bash
# Can you set cache values?
redis-cli SET test "value" && echo "✓ Caching works"

# Can you set TTL?
redis-cli SET key "val" EX 10 && echo "✓ TTL works"

# Can you understand invalidation?
echo "✓ Invalidation strategies understood"

# Can you publish/subscribe?
echo "✓ Queue patterns understood"
```

## Cleanup

```bash
# Flush Redis
redis-cli FLUSHALL

# Stop containers
docker stop rabbitmq kafka 2>/dev/null
docker rm rabbitmq kafka 2>/dev/null
```

## Common Mistakes

1. **Too large cache**: Slows down lookups, wastes memory
2. **No TTL**: Stale data served forever
3. **Cache stampede**: Many requests miss cache at same time
4. **Lost messages**: No persistence in queue
5. **Unbounded queue**: Memory leak when producer > consumer

## Troubleshooting

| Problem | Solution |
|---------|----------|
| High cache miss rate | Check: TTL, eviction policy, key distribution |
| Stale cache data | Set: appropriate TTL, implement invalidation |
| Queue backup | Increase: consumer count, optimize: processing |
| Message loss | Enable: persistence, replication, acknowledge |
| High latency | Monitor: queue depth, consumer lag, processing time |

## Next Steps

1. Move to **14-security-basics** for secure message handling
2. Learn about distributed caching (Memcached, Redis Cluster)
3. Study consumer groups and partitioning
4. Explore dead letter queues and retry policies
5. Learn about cache warming and preloading

## Additional Resources

- Redis: https://redis.io/commands/
- RabbitMQ: https://www.rabbitmq.com/documentation.html
- Kafka: https://kafka.apache.org/documentation/
- Cache patterns: https://en.wikipedia.org/wiki/Cache_(computing)

