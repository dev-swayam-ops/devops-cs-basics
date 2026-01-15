# 12 - Databases for DevOps Cheatsheet

## Database Connection Commands

| Database | Command | Connect | Port |
|----------|---------|---------|------|
| PostgreSQL | psql | `psql -U user -d db` | 5432 |
| MySQL | mysql | `mysql -u user -p db` | 3306 |
| MongoDB | mongo | `mongo localhost:27017` | 27017 |
| Redis | redis-cli | `redis-cli -h localhost` | 6379 |
| SQLite | sqlite3 | `sqlite3 database.db` | N/A |

## SQL Basics

| Command | Purpose | Example |
|---------|---------|---------|
| CREATE TABLE | Define schema | `CREATE TABLE users (...);` |
| INSERT | Add data | `INSERT INTO users VALUES (...);` |
| SELECT | Query data | `SELECT * FROM users;` |
| UPDATE | Modify data | `UPDATE users SET age=30;` |
| DELETE | Remove data | `DELETE FROM users WHERE id=1;` |
| DROP | Delete table | `DROP TABLE users;` |
| ALTER | Change schema | `ALTER TABLE users ADD COLUMN ...;` |

## PostgreSQL Specific

| Command | Purpose | Example |
|---------|---------|---------|
| `psql -l` | List databases | Show all DBs |
| `\l` | List (in psql) | Same as above |
| `\c dbname` | Connect to DB | Switch database |
| `\dt` | List tables | Show tables |
| `\d tablename` | Describe table | Schema details |
| `pg_dump db` | Export database | Backup |
| `pg_restore` | Import database | Restore |

## MySQL Specific

| Command | Purpose | Example |
|---------|---------|---------|
| `SHOW DATABASES;` | List databases | All DBs |
| `USE dbname;` | Select database | Switch DB |
| `SHOW TABLES;` | List tables | All tables |
| `DESCRIBE table;` | Show schema | Column info |
| `mysqldump -u user db` | Backup | Export data |
| `mysql -u user db < file` | Restore | Import data |

## MongoDB Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `db.getDatabase()` | Current database | Show active DB |
| `db.collections` | List collections | Show all |
| `db.collection.find()` | Query | Get all documents |
| `db.collection.insert()` | Add document | Create |
| `db.collection.update()` | Modify | Edit document |
| `db.collection.remove()` | Delete | Remove document |

## Backup Commands

| Tool | Command | Output |
|------|---------|--------|
| pg_dump | `pg_dump db > backup.sql` | SQL script |
| mysqldump | `mysqldump -u user db > backup.sql` | SQL script |
| mongodump | `mongodump -d db -o /path` | Binary format |
| tar | `tar -czf backup.tar.gz /data` | Filesystem |

## Database Types

| Type | Examples | Use Case | Tradeoff |
|------|----------|----------|----------|
| Relational | PostgreSQL, MySQL | Structured data, ACID | Scalability |
| Document | MongoDB, CouchDB | Flexible schema, JSON | Consistency |
| Key-value | Redis, Memcached | Fast lookups, cache | Persistence |
| Time-series | InfluxDB, Prometheus | Metrics, events | Query power |
| Graph | Neo4j | Relationships | Query complexity |

## ACID Properties

| Property | Meaning | Importance |
|----------|---------|-----------|
| Atomicity | All or nothing | Data integrity |
| Consistency | Valid state | No corruption |
| Isolation | No interference | Concurrency |
| Durability | Persisted | Crash recovery |

## Index Types

| Type | Use Case | Speed |
|------|----------|-------|
| Primary Key | Unique identifier | O(log N) |
| Unique | No duplicates | O(log N) |
| Full-text | Text search | Specialized |
| Geo-spatial | Location queries | Specialized |

## Common PostgreSQL Queries

```sql
-- Connection info
SELECT current_user, current_database();

-- Database size
SELECT pg_database_size(current_database());

-- Active connections
SELECT COUNT(*) FROM pg_stat_activity;

-- Slow queries (if pg_stat_statements enabled)
SELECT query, calls, mean_time FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 5;

-- Index usage
SELECT schemaname, tablename, indexname, idx_scan 
FROM pg_stat_user_indexes 
WHERE idx_scan = 0;
```

## Common MySQL Queries

```sql
-- Current user and database
SELECT USER(), DATABASE();

-- Database size
SELECT SUM(DATA_LENGTH + INDEX_LENGTH) 
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'dbname';

-- Active connections
SHOW PROCESSLIST;

-- Slow queries
SHOW VARIABLES LIKE 'slow_query%';

-- Replication status
SHOW SLAVE STATUS\G;
```

## Connection String Formats

```
PostgreSQL:
postgresql://user:password@localhost:5432/dbname

MySQL:
mysql://user:password@localhost:3306/dbname

MongoDB:
mongodb://user:password@localhost:27017/dbname

SQLite:
sqlite:///path/to/database.db

Redis:
redis://localhost:6379/0
```

## Backup Strategies

| Strategy | Frequency | RPO | Cost |
|----------|-----------|-----|------|
| Full | Daily | 1 day | High |
| Incremental | Hourly | 1 hour | Medium |
| Continuous | Real-time | Minutes | High |
| Point-in-time | Varies | Custom | High |

## Performance Tuning

| Task | Command | Effect |
|------|---------|--------|
| Analyze | `ANALYZE;` | Update stats |
| Vacuum | `VACUUM;` | Reclaim space |
| Reindex | `REINDEX;` | Rebuild indexes |
| Check | `CHECK TABLE;` | Find corruption |

## Troubleshooting Commands

```bash
# Test connection
pg_isready
mysql -u root -e "SELECT 1;"
redis-cli ping

# Check logs
tail /var/log/postgresql/postgresql.log
tail /var/log/mysql/error.log

# Monitor connections
watch -n 1 'psql -c "SELECT COUNT(*) FROM pg_stat_activity;"'

# Dump to file
mysqldump -u user db > backup.sql
pg_dump db > backup.sql
```
