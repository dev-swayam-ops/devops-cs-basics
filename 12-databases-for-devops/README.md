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

**Relational (SQL)**: Data in tables, strong schema, ACID guarantees, SQL queries, joins for relationships. Examples: PostgreSQL, MySQL. Use for: structured data, complex queries, data integrity. Trade-off: write scaling hard, schema inflexible.

**Document (NoSQL)**: JSON/BSON documents, schema-less, flexible. Examples: MongoDB, CouchDB. Use for: flexible schema, hierarchical data. Trade-off: weaker consistency, no/slow joins.

**Key-Value**: Simple key→value mapping, O(1) lookup. Examples: Redis, Memcached. Use for: caching, sessions, high-speed access. Trade-off: no querying, no relationships.

**Time-Series**: Optimized for timestamps/metrics with efficient compression. Examples: InfluxDB, Prometheus. Use for: monitoring, metrics, logs.

**Graph**: Nodes and edges for relationships. Example: Neo4j. Use for: social networks, recommendations.

### 2. ACID Properties

**Atomicity**: All-or-nothing transactions. Entire transaction succeeds or entire transaction rolls back. Example: Bank transfer debits and credits both happen or neither. Prevents partial/incomplete operations.

**Consistency**: Database moves from valid state to valid state. Constraints maintained (e.g., foreign keys). Invalid transactions rejected. Ensures referential integrity and business logic correctness.

**Isolation**: Concurrent transactions don't interfere. Transaction T1 doesn't see partial updates or updates from T2. Trade-off: strict isolation (safe) vs loose isolation (faster). Levels: serializable (safest) to read-uncommitted (fastest).

**Durability**: Committed data persists permanently. Survives server crashes (written to disk). Recovery restores committed data on restart.

**Trade-off**: Strict ACID = slower (constraints, disk writes). NoSQL often sacrifices ACID for speed/scale. Choose based on needs: banking requires ACID, caching can skip.

### 3. Backup and Restore

**Full Backup**: Complete database copy. Simple, complete recovery. Trade-off: slow, space-intensive. Use for: small databases, periodic backups.

**Incremental Backup**: Only changes since last backup. Much faster, smaller. Trade-off: complex restore (need all incrementals in order), longer recovery. Use for: large databases, frequent backups.

**Point-in-Time Recovery (PITR)**: Restore to any moment (uses write-ahead logs). Example: "restore to 2024-01-15 14:30:00". Protects against accidental delete/corruption.

**3-2-1 Rule**: 3 copies (original + 2 backups), 2 storage types (disk + tape), 1 offsite. Test restores regularly. Backup frequency = RPO (Recovery Point Objective). Restore time = RTO (Recovery Time Objective).

**Tools**: pg_dump (PostgreSQL), mysqldump (MySQL), mongodump (MongoDB).

### 4. Database Connections

**Connection Components**:
- **Host**: IP or hostname (e.g., "db.example.com" or "192.168.1.100"). Can be load-balanced.
- **Port**: TCP port database listens (defaults: 3306 MySQL, 5432 PostgreSQL, 27017 MongoDB).
- **Credentials**: Username + password with access permissions. Never hardcode—use environment variables.
- **Database**: Logical container (table schema in SQL, document collection in NoSQL).

**Connection Pool**: Reuse N open connections instead of creating new ones (expensive). Typical size: 5-20. Advantages: faster, less resource. Disadvantage: state complexity. Create connections before application starts.

### 5. Basic Database Operations

**CRUD Operations**:
- **Create** (INSERT): Add new records. Atomic: all or fails.
- **Read** (SELECT): Query and retrieve. Non-destructive.
- **Update**: Modify existing records. Can affect single or multiple rows.
- **Delete**: Remove data. Usually logged for recovery.

**Backup/Export & Restore/Import**: Export database to file (SQL, JSON). Tools: pg_dump, mysqldump, mongodump. Restore loads from file. Validate integrity after restore.

**Monitoring**: Health (running?), performance (slow queries?), capacity (disk/connections/memory?), replication (lag?). Tools: built-in monitoring, Prometheus, Datadog.

**Common Operational Tasks**: Start/stop service (systemctl), user management (create users, grant perms), index management (create/drop/analyze), maintenance (VACUUM cleanup, ANALYZE stats), replication setup.

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

