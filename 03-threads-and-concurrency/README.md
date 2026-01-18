# 03 - Threads and Concurrency

## What You'll Learn

- Thread basics and creation
- Synchronization primitives (mutex, semaphore)
- Race conditions and deadlocks
- Critical sections and atomic operations
- Thread pools and work distribution
- Performance implications of concurrency

## Prerequisites

- Completed **02-process-management**
- Understanding of processes and CPU
- Basic shell scripting knowledge

## Key Concepts

### 1. Threads vs Processes

**Threads** (lightweight, shared memory)
- Multiple execution flows within one process
- Share memory → cheap communication, but risk of data corruption
- Fast context switching
- Used for concurrent I/O (web servers handling multiple requests)

**Processes** (heavyweight, isolated)
- Completely separate execution environments
- Separate memory → safe isolation, but expensive communication
- Used for independent applications or security boundaries

**DevOps Relevance**: Understanding this helps diagnose resource usage and performance bottlenecks. Modern servers use threads for concurrency.

### 2. Race Conditions

**Problem**: Multiple threads accessing shared data simultaneously → unpredictable results, data corruption, hard-to-debug failures.

**Example**: Two threads increment counter from 5 → both read 5, both write 6 (one increment lost!).

**Solution**: Use locks (mutexes) to ensure only one thread accesses shared data at a time.

### 3. Synchronization Primitives

**Mutex (Lock)**
- Only one thread can hold at a time
- Thread waits if already locked
- Use: Protect critical sections (shared data access)

**Semaphore**
- Counter-based: allows N threads to access resource simultaneously
- `wait()` decrements counter, blocks if 0
- `signal()` increments counter
- Use: Control access to resource pools (e.g., 5 database connections available)

### 4. Deadlock

**What**: Threads stuck waiting for each other's locks → system freezes, no error raised.

**Example**: Thread A holds lock X, waits for Y. Thread B holds Y, waits for X. Both blocked forever.

**Prevention**:
- **Lock ordering**: Always acquire locks in same order (avoid circular waits)
- **Timeouts**: Don't wait indefinitely for locks
- **Thread pools**: Limit concurrent threads, avoid resource exhaustion

## Hands-on Lab: Thread Synchronization

### Lab Overview
Demonstrate race conditions and synchronization with file locking.

### Lab Commands

```bash
# 1. Create counter file
COUNTER_FILE="/tmp/counter.txt"
echo "0" > $COUNTER_FILE

# 2. Define increment function with lock
increment() {
  exec 200>"$COUNTER_FILE.lock"
  flock 200  # Acquire lock
  count=$(cat $COUNTER_FILE)
  count=$((count + 1))
  echo $count > $COUNTER_FILE
  flock -u 200  # Release lock
  exec 200>&-
}

# 3. Run 5 concurrent increments
for i in {1..5}; do increment & done
wait

# 4. Check result
cat $COUNTER_FILE
# Expected: 5 (all increments applied)

# 5. View thread information
ps -eLf | head -10
# Expected: Shows thread IDs (LWP column)

# 6. Check thread count of process
grep Threads /proc/$$/status
# Expected: Threads: 1 (current shell)

# 7. Demonstrate race without lock (create script)
cat > /tmp/race.sh << 'EOF'
count=0
for i in {1..100}; do
  count=$((count + 1))
done
echo $count
EOF

# 8. Run concurrent without synchronization
bash /tmp/race.sh & bash /tmp/race.sh &
wait
# Expected: Both show 100 (separate memory spaces)

# 9. Show lock file in use
ls -la $COUNTER_FILE.lock 2>/dev/null
# Expected: -rw-r--r-- (lock file exists)

# 10. Cleanup
rm -f $COUNTER_FILE $COUNTER_FILE.lock /tmp/race.sh
```

## Common Mistakes

1. **Forgetting to lock**: Shared data always needs synchronization
2. **Holding locks too long**: Reduces parallelism
3. **Nested locks**: Can cause deadlock if wrong order
4. **No timeout**: Can wait forever in deadlock
5. **Busy-waiting**: Wastes CPU, use sleep instead

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Deadlock (processes hung) | Kill processes, review lock ordering |
| Race condition (inconsistent results) | Add synchronization locks |
| Low performance | Reduce lock time, use RWLock instead |
| Thread explosion | Use thread pool, limit concurrent |
| Data corruption | Verify all accesses protected by locks |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for reference
4. Move to **04-memory-management** after completion

## Additional Resources

- POSIX threads: `man pthreads`
- File locking: `man flock`

