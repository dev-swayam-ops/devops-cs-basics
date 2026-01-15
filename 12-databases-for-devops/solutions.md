# 12 - Databases for DevOps Solutions

## Easy Solutions (1-5)

### Solution 1: Database Type Selection

**Matching**:
- User profiles: SQL (ACID, relationships)
- Metrics: Time-series (fast writes)
- Cache layer: Key-value (ultra-fast)
- API responses: NoSQL (flexible)
- Financial transactions: SQL (ACID)

---

### Solution 2: Connect to Database

**PostgreSQL**:
```bash
pg_isready -h localhost -p 5432
psql -U postgres -h localhost
```

**MySQL**:
```bash
mysql -u root -p -h localhost
```

**Requirements**:
- Host: server location
- Port: 5432 (PG), 3306 (MySQL)
- Credentials: user, password
- Optional: database name

---

### Solution 3: List Databases

**PostgreSQL**:
```bash
psql -U postgres -l
# Or inside psql: \l
```

**MySQL**:
```bash
mysql -u root -e "SHOW DATABASES;"
```

**Output**:
```
postgres - system database
template0, template1 - templates
testdb - user database
```

---

### Solution 4: Create Table

**Script**:
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  price DECIMAL(10,2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Definitions**:
- PRIMARY KEY: unique identifier
- VARCHAR: variable string
- DECIMAL: exact precision (money)
- TIMESTAMP: date/time

---

### Solution 5: CRUD Operations

**Script**:
```sql
-- Create
INSERT INTO products (id, name, price) VALUES (1, 'Widget', 9.99);

-- Read
SELECT * FROM products;
SELECT name, price FROM products WHERE id=1;

-- Update
UPDATE products SET price=10.99 WHERE id=1;

-- Delete
DELETE FROM products WHERE id=1;
```

**Persistence**: Data survives connection close (persisted to disk)

---

## Medium Solutions (6-10)

### Solution 6: Backup and Restore

**PostgreSQL**:
```bash
# Backup
pg_dump mydb > backup.sql

# Restore
psql mydb < backup.sql

# Or: pg_restore from binary dump
pg_dump -F custom mydb > backup.dump
pg_restore -d mydb backup.dump
```

**Size**:
- Depends on data volume
- Compression: reduces size
- Incremental: only changes

---

### Solution 7: Connection Pooling

**Check connections**:
```sql
SELECT COUNT(*) FROM pg_stat_activity;  -- PostgreSQL
SHOW PROCESSLIST;  -- MySQL
```

**Pooling**:
```
Without pool: New connection each request (overhead)
With pool: Reuse connections (faster)
Example: PgBouncer, MySQL Proxy
```

---

### Solution 8: Query Optimization

**Analyze**:
```sql
EXPLAIN SELECT * FROM users WHERE id=1;
-- Shows execution plan, cost estimate

EXPLAIN ANALYZE SELECT * FROM users WHERE id=1;
-- Shows actual execution time
```

**Index**:
```sql
CREATE INDEX idx_users_id ON users(id);
-- Makes lookups faster (O(log N))
```

---

### Solution 9: Replication Lag

**Check lag**:
```sql
-- PostgreSQL
SELECT now() - pg_last_wal_receive_lsn_time() as lag;

-- MySQL
SHOW SLAVE STATUS\G
-- Look for: Seconds_Behind_Master
```

**Behavior**:
- Write to master: immediate
- Read from replica: may be stale
- Use: eventually consistent reads only

---

### Solution 10: Monitor Health

**PostgreSQL**:
```sql
-- Active connections
SELECT count(*) FROM pg_stat_activity;

-- Database size
SELECT pg_database_size(current_database());

-- Slow queries
SELECT query, calls, mean_time FROM pg_stat_statements
ORDER BY mean_time DESC LIMIT 5;
```

**Key metrics**:
- Connections: approaching limit?
- Size: disk usage
- Slow queries: need optimization?

---

## Quick Reference

```bash
# PostgreSQL
psql -U user -d dbname
pg_dump dbname > backup.sql
pg_restore -d dbname backup.dump

# MySQL
mysql -u user -p dbname
mysqldump -u user dbname > backup.sql

# Check health
SELECT 1;  -- Simple test
```
