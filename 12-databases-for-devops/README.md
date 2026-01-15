# 12 - Databases for DevOps

## What You'll Learn

- SQL vs NoSQL database types
- Relational databases (PostgreSQL, MySQL)
- Document databases (MongoDB)
- In-memory databases (Redis)
- Backup and restore
- Database connection and queries
- Monitoring database health
- Common operational tasks

## Prerequisites

- Completed **11-distributed-systems-basics**
- Basic understanding of data
- Familiarity with networks

## Key Concepts

### 1. Database Types
```
Relational (SQL): Tables, ACID
- PostgreSQL, MySQL, Oracle

Document (NoSQL): JSON documents
- MongoDB, CouchDB

Key-Value: Fast lookup
- Redis, Memcached

Time-Series: Metrics
- InfluxDB, Prometheus

Graph: Relationships
- Neo4j
```

### 2. ACID Properties
- **Atomicity**: All or nothing
- **Consistency**: Valid state
- **Isolation**: No interference
- **Durability**: Persisted

### 3. Backup Types
- **Full**: Complete copy
- **Incremental**: Changes only
- **Point-in-time**: Specific moment

### 4. Connection
```
Host: database server IP
Port: 3306 (MySQL), 5432 (PostgreSQL), 27017 (MongoDB)
Credentials: username, password
Database: logical collection
```

### 5. Basic Operations
- CRUD: Create, Read, Update, Delete
- Backup: Export data
- Restore: Import data
- Monitor: Check health

## Hands-on Lab: Database Connection and Query

### Lab Overview
Connect to database and perform basic operations.

### Lab Commands

```bash
# 1. Start PostgreSQL (if installed)
sudo systemctl start postgresql

# 2. Check if running
pg_isready

# Expected: accepting connections

# 3. Connect as user
psql -U postgres -h localhost

# 4. Inside psql, create database
CREATE DATABASE testdb;

# 5. List databases
\l

# Expected: (list of databases)

# 6. Connect to database
\c testdb

# 7. Create table
CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(100));

# 8. Insert data
INSERT INTO users VALUES (1, 'Alice');
INSERT INTO users VALUES (2, 'Bob');

# 9. Query data
SELECT * FROM users;

# Expected:
# id | name
# 1  | Alice
# 2  | Bob

# 10. Backup database
pg_dump testdb > testdb_backup.sql

# (Or: mysqldump for MySQL)
# (Or: mongodump for MongoDB)
```

## Validation

```bash
# Can you connect to database?
pg_isready && echo "✓ Database ready"

# Create table?
echo "✓ Schema created"

# Insert data?
echo "✓ Data inserted"

# Backup database?
test -f testdb_backup.sql && echo "✓ Backup created"
```

## Cleanup

```bash
# Drop database (careful!)
# psql -U postgres -c "DROP DATABASE testdb;"

# Restore from backup
# psql -U postgres < testdb_backup.sql

# Remove backup file
rm -f testdb_backup.sql
```

## Common Mistakes

1. **Credentials exposed**: Don't hardcode passwords
2. **No backups**: Always backup before changes
3. **Blocking queries**: Long queries block others
4. **Connection leaks**: Close connections properly
5. **Replica lag**: Replication delay = stale data

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't connect | Check: port, hostname, credentials |
| Slow queries | Use: EXPLAIN ANALYZE, optimize |
| Backup failed | Check: disk space, permissions |
| High memory | Check: query cache, connections |
| Replication lag | Monitor: replication status |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **13-caching-and-queues** after completion

## Additional Resources

- PostgreSQL: `man psql`, postgresql.org
- MySQL: `man mysql`, mysql.com
- MongoDB: `man mongo`, mongodb.com
- Redis: redis.io/commands

