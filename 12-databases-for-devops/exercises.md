# 12 - Databases for DevOps Exercises

## Easy Exercises (1-5)

### Exercise 1: Understand Database Types
**Objective**: Know when to use each.

**Task**:
- SQL: structured, ACID (PostgreSQL)
- NoSQL: flexible, fast (MongoDB)
- Key-value: ultra-fast (Redis)
- Time-series: metrics (InfluxDB)
- Which for: user profiles? metrics? cache?

---

### Exercise 2: Connect to Database
**Objective**: Basic connection.

**Task**:
- Check service: `pg_isready` or `mysql -u root -e "SELECT 1;"`
- Connect: `psql -U user -h localhost`
- Or: `mysql -u root -p`
- What's required: host, port, credentials?

---

### Exercise 3: List Databases
**Objective**: See available databases.

**Task**:
- PostgreSQL: `\l` (inside psql)
- MySQL: `SHOW DATABASES;`
- How many? (system vs user databases)
- Which is default?

---

### Exercise 4: Create Table
**Objective**: Define schema.

**Task**:
```sql
CREATE TABLE products (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  price DECIMAL(10,2)
);
```
- What's PRIMARY KEY?
- VARCHAR vs INT difference?
- Can you delete table? (DROP TABLE)

---

### Exercise 5: Insert and Query
**Objective**: CRUD basics.

**Task**:
```sql
INSERT INTO products VALUES (1, 'Widget', 9.99);
SELECT * FROM products;
UPDATE products SET price=10.99 WHERE id=1;
DELETE FROM products WHERE id=1;
```
- Understand each: CREATE, READ, UPDATE, DELETE?
- Data persists? (after query exit)

---

## Medium Exercises (6-10)

### Exercise 6: Backup and Restore
**Objective**: Protect data.

**Task**:
- PostgreSQL backup: `pg_dump db > backup.sql`
- MySQL backup: `mysqldump -u root db > backup.sql`
- How large is backup?
- Can you restore? (psql < backup.sql)
- Incremental backup vs full?

---

### Exercise 7: Connection Pooling
**Objective**: Understand connection limits.

**Task**:
- Each connection: memory and resources
- Too many: database overloaded
- Pool: reuse connections
- How many connections open now?
- Monitor: `SELECT COUNT(*) FROM pg_stat_activity;`

---

### Exercise 8: Query Optimization
**Objective**: Find slow queries.

**Task**:
- Explain query: `EXPLAIN SELECT * FROM large_table;`
- Shows: execution plan, cost
- Index: faster lookups (CREATE INDEX)
- Slow query without index?
- Fast with index? (verify)

---

### Exercise 9: Replication Lag
**Objective**: Monitor replica freshness.

**Task**:
- Master writes: immediately
- Replica copies: delayed
- Check lag: database-specific command
- Write to master, read from replica
- Data might be stale?

---

### Exercise 10: Monitor Database Health
**Objective**: Operational visibility.

**Task**:
- Active connections: `pg_stat_activity`
- Size: `SELECT pg_database_size(current_database());`
- Slow queries: `pg_stat_statements`
- What metric most important?

---

## Submission Tips

1. Always backup before changes
2. Test restore procedure
3. Monitor slow queries
4. Use indexes for frequent searches
5. Connection pooling for production
