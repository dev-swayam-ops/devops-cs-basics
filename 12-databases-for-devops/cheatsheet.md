# 12 - Databases for DevOps: Cheatsheet

## Database Types

| Type | Examples | Best For |
|------|----------|----------|
| **SQL** | PostgreSQL, MySQL | Structured data, ACID |
| **NoSQL** | MongoDB, Cassandra | Unstructured, scale-out |
| **Key-Value** | Redis, Memcached | Caching, sessions |
| **Time-Series** | InfluxDB, Prometheus | Metrics, monitoring |
| **Search** | Elasticsearch | Full-text search |
| **Graph** | Neo4j | Relationships, recommendations |

## SQL Commands

```bash
# Backup and restore
mysqldump -u user -p database > backup.sql
mysql -u user -p database < backup.sql

# Check table size
SELECT table_name, ROUND(data_length/1024/1024) AS size_mb FROM information_schema.tables;

# Index optimization
EXPLAIN SELECT * FROM table WHERE condition;
CREATE INDEX idx_name ON table(column);

# Replication status
SHOW SLAVE STATUS;
SHOW MASTER STATUS;
```

## NoSQL Design

```
Document Design:
- Denormalize for performance
- Avoid joins
- Plan for updates

Sharding Key:
- Even distribution
- Avoid hot spots
- Immutable

Consistency:
- Write concern level
- Read preference
```

## Performance Tuning

```bash
# Query analysis
EXPLAIN ANALYZE SELECT ...;

# Index usage
SELECT * FROM pg_stat_user_indexes;

# Cache hit ratio
SELECT sum(heap_blks_read) / (sum(heap_blks_read) + sum(heap_blks_hit)) AS cache_hit_ratio FROM pg_statio_user_tables;
```

## Monitoring

```
Key Metrics:
- Query latency (p50, p95, p99)
- Connection pool usage
- Replication lag
- Disk usage
- CPU/Memory
```

---
