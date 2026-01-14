# 12 - Databases for DevOps

## What You'll Learn

Master database concepts for DevOps:
- Relational vs NoSQL databases
- Data modeling and schema design
- Indexing and query optimization
- Replication and backups
- Connection pooling
- Database monitoring
- Common database engines

## Prerequisites

- Completed **11-distributed-systems-basics**
- SQL basics helpful
- System architecture understanding

## Key Concepts

### 1. Database Types
- **Relational (SQL)**: PostgreSQL, MySQL, MariaDB - structured data, ACID
- **Key-Value**: Redis, Memcached - fast caching, simple operations
- **Document**: MongoDB, CouchDB - flexible schema, JSON-like
- **Time-Series**: InfluxDB, Prometheus - metrics and logs
- **Search**: Elasticsearch - full-text search and analytics

### 2. Indexes
- Accelerate queries by pre-sorting data
- B-tree most common structure
- Trade-off: read speed vs write speed and storage
- Multiple indexes can hurt performance

### 3. Replication
- **Synchronous**: Slow but safe (all replicas written)
- **Asynchronous**: Fast but risky (replicas may lag)
- **Semi-sync**: Balance between both
- Write to leader, read from followers (read replicas)

### 4. Backup Strategies
- **Full backup**: Complete copy, large, slow recovery
- **Incremental**: Only changes, small, complex recovery
- **Differential**: Changes since last full, balance
- **Point-in-time recovery**: Combine backups + transaction logs

## Hands-on Lab: Database Operations

### Lab Steps

```bash
# 1. Start MySQL/MariaDB
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=pass -p 3306:3306 mysql:8.0

# 2. Connect to database
mysql -h localhost -u root -ppass

# 3. Create and query
mysql> CREATE DATABASE testdb;
mysql> USE testdb;
mysql> CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(100));
mysql> INSERT INTO users VALUES (1, 'John');
mysql> SELECT * FROM users;

# 4. Check indexes
mysql> SHOW INDEXES FROM users;
mysql> EXPLAIN SELECT * FROM users WHERE id=1;

# 5. Monitor connections
mysql> SHOW PROCESSLIST;
mysql> SHOW STATUS LIKE 'Threads%';

# 6. Backup database
mysqldump -u root -ppass testdb > backup.sql

# 7. Restore from backup
mysql -u root -ppass testdb < backup.sql

# 8. Check replication status
mysql> SHOW MASTER STATUS;

# 9. Redis for caching
redis-cli > SET key1 "value1"
redis-cli > GET key1
redis-cli > DEL key1

# 10. Check database size
du -sh /var/lib/mysql/
```

## Validation

Verify your database knowledge:

```bash
# Can you connect to database?
mysql -h localhost -u root -ppass -e "SELECT 1;" && echo "✓ DB connection works"

# Can you create tables?
mysql -h localhost -u root -ppass testdb -e "CREATE TABLE test (id INT);" && echo "✓ DDL works"

# Can you query data?
mysql -h localhost -u root -ppass testdb -e "SELECT * FROM test;" && echo "✓ Queries work"

# Can you backup?
mysqldump -u root -ppass testdb > /dev/null && echo "✓ Backup works"
```

## Cleanup

```bash
# Drop database
mysql -u root -ppass -e "DROP DATABASE testdb;"

# Stop container
docker stop mysql && docker rm mysql

# Remove backup
rm -f backup.sql
```

## Common Mistakes

1. **No indexes**: Queries slow on large datasets
2. **Too many indexes**: Slows down writes, uses memory
3. **Async replication**: Data loss if leader dies
4. **Single server**: No redundancy, single point of failure
5. **No backups**: Can't recover from disaster

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Slow queries | Add: indexes, optimize: schema, check: table locks |
| Replication lag | Reduce: write load, increase: slave resources |
| High memory | Check: cache size, active connections, indexes |
| Connection refused | Verify: service running, port listening, firewall |
| Data corruption | Restore: from backup, check: replication |

## Next Steps

1. Move to **13-caching-and-queues** for performance
2. Learn about database scaling (sharding, read replicas)
3. Study backup and recovery procedures
4. Explore database clustering
5. Learn about connection pooling (pgBouncer, ProxySQL)

## Additional Resources

- MySQL: https://dev.mysql.com/doc/
- PostgreSQL: https://www.postgresql.org/docs/
- Redis: https://redis.io/commands/
- Database design: https://en.wikipedia.org/wiki/Database_design

