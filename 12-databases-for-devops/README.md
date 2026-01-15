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

Different database types optimize for different access patterns. Choosing the right type is critical for performance.

**Relational Databases (SQL)**
- Data organized in tables with rows and columns
- Strong schema: structure defined upfront
- ACID guarantees: data integrity
- SQL query language: powerful, standardized
- Joins: efficiently combine related data
- Examples: PostgreSQL, MySQL, Oracle, SQL Server
- Use when: structured data, complex queries, data integrity critical
- Advantages: proven, reliable, standardized
- Disadvantages: scaling writes hard, schema inflexible

**Document Databases (NoSQL)**
- Data stored as JSON/BSON documents
- Schema-less: each document can have different structure
- Flexible but less safe
- Examples: MongoDB, CouchDB, Firebase
- Natural fit for: application objects, nested data
- Use when: flexible schema, hierarchical data, rapid iteration
- Advantages: flexible, horizontal scaling, developer-friendly
- Disadvantages: weaker consistency, no joins (or slow)

**Key-Value Databases**
- Simple: key → value mapping
- Fastest access: O(1) lookup
- No queries: get/set/delete operations
- Examples: Redis, Memcached, DynamoDB
- Use when: caching, sessions, high-speed access, leaderboards
- Advantages: extremely fast, simple
- Disadvantages: no querying, no relationships

**Time-Series Databases**
- Optimized for metrics and time-stamped data
- Each value has timestamp
- Efficient compression: similar values stored compactly
- Examples: InfluxDB, Prometheus, TimescaleDB
- Use when: monitoring, metrics, logs, sensor data
- Advantages: efficient storage, fast aggregations
- Disadvantages: specialized, not general-purpose

**Graph Databases**
- Data as nodes and edges (relationships)
- Efficient relationship traversal
- Examples: Neo4j, ArangoDB
- Use when: social networks, recommendations, knowledge graphs
- Advantages: fast relationship queries
- Disadvantages: specialized, learning curve

### 2. ACID Properties

ACID guarantees ensure data integrity and reliability. Understanding each property is critical for database operations.

**Atomicity**
- Transaction is "all or nothing"
- All operations in transaction succeed, or all roll back
- No partial completion
- Example: Bank transfer: debit account AND credit account both happen, or neither
- If server crashes mid-transaction: both reverted on recovery
- Ensures: no money lost, no money duplicated

**Consistency**
- Database moves from valid state to valid state
- Constraints always maintained
- Example: foreign key constraint: can't reference non-existent user
- After transaction: database in consistent state
- If invalid transaction attempted: rejected
- Ensures: referential integrity, business logic correctness

**Isolation**
- Concurrent transactions don't interfere
- Transaction T1 sees neither partial updates nor updates from T2
- Feels like transactions run serially (one after another)
- Levels: serializable (safest) to read-uncommitted (fastest)
- Example: two users updating same row simultaneously: isolated from each other
- Trade-off: isolation vs concurrency (too strict slows down)

**Durability**
- Once committed, data persisted permanently
- Even if server crashes: data survives
- Written to disk, not just memory
- Recovery: on restart, committed data restored
- Example: Committed transaction survives power loss

**ACID Trade-offs**
- Strict ACID: slower (must check constraints, log to disk)
- NoSQL databases often sacrifice ACID for speed/scale
- Some databases have weaker consistency models
- Choose based on requirements: banking needs ACID, caching can skip

### 3. Backup and Restore

Backups are critical for disaster recovery. Different strategies provide different guarantees and recovery speed.

**Full Backup**
- Complete copy of entire database
- Contains all data as of backup time
- Advantages: simple, complete recovery
- Disadvantages: slow (copy entire database), space-intensive
- Use when: small database, periodic backups acceptable
- Recovery: restore single backup file

**Incremental Backup**
- Only copy changes since last backup
- Much faster and smaller
- Advantages: efficient, suitable for large databases
- Disadvantages: complex restore (need all incrementals), longer recovery
- Use when: large database, frequent backups needed
- Recovery: restore last full, then apply all incrementals in order

**Point-in-Time Recovery (PITR)**
- Restore database to any moment in time
- Uses write-ahead log (WAL): record of all changes
- Example: "restore to 2024-01-15 14:30:00"
- Advantages: recover from accidental delete/corruption
- Disadvantages: requires log storage, recovery time varies
- Use when: protection against human error needed

**Backup Strategy (3-2-1 Rule)**
- 3 copies of data (original + 2 backups)
- 2 different storage types (disk + tape)
- 1 offsite (protect against datacenter disaster)
- Regular restore testing: ensure backups actually work
- Backup frequency: RPO (Recovery Point Objective) determines needed frequency

**Restoration Considerations**
- Restore time: RTO (Recovery Time Objective) determines acceptable restore duration
- Validation: verify restored data integrity
- Capacity: restore destination needs enough space
- Dependencies: restore in correct order if databases related

### 4. Database Connections

Proper connection management is essential for reliability and performance.

**Connection Components**

**Host**
- IP address or hostname of database server
- Example: "db.example.com" or "192.168.1.100"
- DNS resolution: hostname translated to IP
- Can be load-balanced (multiple servers)

**Port**
- TCP port database listens on
- Default ports: 3306 (MySQL), 5432 (PostgreSQL), 27017 (MongoDB)
- Custom ports: can be changed for security
- Example: connection to "db.example.com:5432"

**Credentials**
- Username: account to use ("postgres", "root", etc.)
- Password: authentication
- Permissions: user can only access certain databases/tables
- Security: never hardcode credentials, use environment variables

**Database**
- Logical container for tables (relational)
- Or collection of documents (document databases)
- User may have access to multiple databases
- Example: connect to specific database "myapp_db"

**Connection Pool**
- Reuse connections instead of creating new ones
- Creating connection expensive (handshake, authentication)
- Pool maintains N open connections, reuses them
- Advantages: faster, less resource usage
- Disadvantages: complex state management
- Size: typically 5-20 connections per application

### 5. Basic Database Operations

Understanding CRUD and operational tasks is essential for DevOps.

**CRUD Operations**

**Create**
- Insert new data
- SQL: INSERT statement
- MongoDB: insertOne/insertMany
- Atomic: entire record inserted or fails

**Read**
- Query and retrieve data
- SQL: SELECT statement with filters
- MongoDB: find() with query
- Multiple reads don't modify data

**Update**
- Modify existing data
- SQL: UPDATE statement
- MongoDB: updateOne/updateMany
- Can affect single row or multiple

**Delete**
- Remove data
- SQL: DELETE statement
- MongoDB: deleteOne/deleteMany
- Usually logged for recovery

**Backup and Export**
- Export: dump database to file (SQL, JSON, etc.)
- Backup: snapshot for recovery
- Tools: pg_dump, mysqldump, mongodump
- Schedule: automated nightly backups

**Restore and Import**
- Import: load data from file
- Restore: recover from backup
- Tools: psql, mysql, mongorestore
- Validation: verify data integrity after restore

**Monitoring**
- Check health: is database running?
- Performance: slow queries, bottlenecks
- Capacity: disk space, connections, memory
- Replication: lag, synchronization
- Tools: built-in monitoring, external monitoring (Prometheus, Datadog)

**Common Operational Tasks**
- Start/stop service: systemctl start postgresql
- User management: create users, grant permissions
- Index management: create, drop, analyze
- Maintenance: VACUUM (cleanup), ANALYZE (statistics)
- Replication setup: configure master-slave replication

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

