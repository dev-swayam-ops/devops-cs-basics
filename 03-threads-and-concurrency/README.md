# 03 - Threads and Concurrency

## What You'll Learn

Master concurrent programming:
- Thread basics and creation
- Synchronization primitives (mutex, semaphore)
- Race conditions and deadlocks
- Critical sections and atomic operations
- Thread pools and work queues
- Performance considerations

## Prerequisites

- Completed **02-process-management**
- Understanding of processes and context switching
- Basic C or Python knowledge helpful

## Key Concepts

### 1. Threads vs Processes
- **Threads**: Share same memory space, light-weight
- **Processes**: Separate memory, heavier-weight
- **Advantage**: Threads are faster to create/switch
- **Disadvantage**: Threads need synchronization

### 2. Race Conditions
Without synchronization, two threads reading/writing shared data can corrupt it. Example:
- Thread 1: Read X=5, Write X=6
- Thread 2: Read X=5, Write X=6
- Result: X=6 (should be 7!)

With mutex: Only one thread accesses critical section at a time

### 3. Synchronization Primitives
- **Mutex**: Mutual exclusion, allows one thread at a time
- **Semaphore**: Counter-based, allows N threads
- **RWLock**: Multiple readers OR one writer
- **Condition Variable**: Thread signaling

### 4. Deadlock Prevention
- Lock ordering: Always acquire locks in same order
- Timeouts: Don't wait forever
- Resource limits: Prevent circular wait

## Hands-on Lab: Thread Synchronization

### Lab Steps

```bash
# 1. Create a critical section with flock (file locking)
COUNTER_FILE="/tmp/counter.txt"
echo "0" > $COUNTER_FILE

increment_counter() {
  exec 200>"$COUNTER_FILE.lock"
  flock 200
  count=$(cat $COUNTER_FILE)
  count=$((count + 1))
  echo $count > $COUNTER_FILE
  flock -u 200
  exec 200>&-
}

# 2. Run concurrent increments
for i in {1..10}; do
  increment_counter &
done
wait
echo "Final counter: $(cat $COUNTER_FILE)"

# 3. View thread information
ps -eLf | head -20

# 4. Check thread count for process
cat /proc/$$status | grep Threads

# 5. Demonstrate mutex with lock file
lock_file="/tmp/resource.lock"

worker() {
  pid=$1
  for i in {1..5}; do
    while ! mkdir "$lock_file" 2>/dev/null; do
      sleep 0.1
    done
    echo "Worker $pid: critical section"
    sleep 0.5
    rmdir "$lock_file"
  done
}

# 6. Start workers
for i in {1..3}; do
  worker $i &
done
wait

# 7. Monitor thread creation
watch -n 1 'ps -eLf | wc -l'

# 8. Use semaphore pattern (max N concurrent)
semaphore_file="/tmp/semaphore"
max_threads=2
echo "" > $semaphore_file

# 9. Check thread state
ps -eo pid,tid,comm

# 10. Cleanup
rm -f /tmp/counter.* /tmp/resource.lock /tmp/semaphore
```

## Validation

Verify your understanding:

```bash
# Can you identify race conditions?
echo "✓ Race condition concept understood"

# Can you use file locks?
flock -x /tmp/test.lock echo "locked" && echo "✓ Locking works"

# Can you check thread count?
ps -eLf | head -2 && echo "✓ Thread viewing works"

# Can you understand deadlock?
echo "✓ Deadlock prevention understood"
```

## Cleanup

```bash
killall worker 2>/dev/null
rm -f /tmp/counter.* /tmp/resource.lock /tmp/semaphore /tmp/contested.lock /tmp/test.lock
```

## Common Mistakes

1. **Forgetting to lock**: Every shared resource needs synchronization
2. **Holding locks too long**: Impacts performance
3. **Nested locks**: Can cause deadlock (lock ordering!)
4. **No timeout**: Threads can wait forever
5. **Resource leaks**: Must unlock even on exceptions

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Deadlock | Check: lock ordering, timeouts |
| Race condition | Add: mutex locks around critical section |
| Slow threads | Reduce: lock contention, use RWLock |
| Thread explosion | Use: thread pool, limit concurrent threads |
| Data corruption | Verify: all accesses protected by locks |

## Next Steps

1. Move to **04-memory-management** for memory safety
2. Learn about lock-free programming
3. Study thread pools and executors
4. Explore async/await patterns
5. Learn about distributed locks (Redis, etcd)

## Additional Resources

- POSIX threads: `man pthreads`
- File locking: `man flock`
- Thread synchronization: `man pthread_mutex_*`
- Concurrent programming: https://www.1e6.org/onlinebooks/ProgrammingUNIX/

