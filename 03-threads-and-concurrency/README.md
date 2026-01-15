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
- **Threads**: Share same memory space, lightweight
- **Processes**: Separate memory, heavier weight
- **Advantage**: Threads cheaper to create/switch
- **Disadvantage**: Threads need synchronization

### 2. Race Conditions
- Multiple threads access shared data simultaneously
- Result depends on execution order (unpredictable)
- Solution: Use locks/mutex to serialize access
- Example: Two threads incrementing same counter

### 3. Synchronization Primitives
- **Mutex**: Binary lock (locked or unlocked)
- **Semaphore**: Counter-based (allows N threads)
- **Lock Ordering**: Always acquire in same order to prevent deadlock
- **Critical Section**: Code block needing protection

### 4. Deadlock Prevention
- All threads wait for resources held by others
- Lock ordering: acquire locks in consistent order
- Timeouts: don't wait indefinitely
- Resource limits: prevent circular dependencies

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

## Validation

Verify your understanding:

```bash
# Understand race conditions?
echo "✓ Race condition concept understood"

# Can use file locks?
flock -x /tmp/test.lock echo "locked" && echo "✓ Locking works"

# Understand thread states?
ps -eLf | head && echo "✓ Thread viewing works"

# Know deadlock prevention?
echo "✓ Lock ordering concept understood"
```

## Cleanup

```bash
killall bash 2>/dev/null
rm -f /tmp/counter.* /tmp/race.sh /tmp/test.lock
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
- Concurrency: https://en.wikipedia.org/wiki/Concurrency_(computer_science)

