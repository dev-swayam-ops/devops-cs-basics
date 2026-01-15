# 03 - Threads and Concurrency Solutions

## Easy Solutions (1-5)

### Solution 1: Understand Race Condition

**Script**:
```bash
cat > /tmp/race_test.sh << 'EOF'
count=0
for i in {1..1000}; do
  count=$((count + 1))
done
echo $count
EOF

# Run two concurrently
bash /tmp/race_test.sh &
bash /tmp/race_test.sh &
wait
```

**Expected Output**:
```
1000
1000
```

**Explanation**:
- Each bash runs separately (different memory space)
- No race condition between separate processes
- Same script different times shows same result (1000)
- True race condition needs shared memory (threads/processes sharing data)

---

### Solution 2: Use File Locking

**Script**:
```bash
COUNTER="/tmp/count.txt"
echo "0" > $COUNTER

increment_safe() {
  exec 200>"$COUNTER.lock"
  flock 200
  val=$(cat $COUNTER)
  val=$((val + 1))
  echo $val > $COUNTER
  flock -u 200
  exec 200>&-
}

for i in {1..5}; do
  increment_safe &
done
wait
echo "Final: $(cat $COUNTER)"
```

**Expected Output**:
```
Final: 5
```

**Explanation**:
- `flock 200` acquires lock on file descriptor 200
- Only one process increments at a time
- Result is consistent (always 5)
- Without lock, result varies (1-5)

---

### Solution 3: Compare Thread vs Process

**Commands**:
```bash
# Show current shell
ps -eLf | grep "^$(whoami)" | grep bash | head -1

# Thread count in current shell
grep Threads /proc/$$/status

# Start background bash
bash &
PID=$!

# Check its threads
grep Threads /proc/$PID/status

# Check memory
ps -o pid,vsz,rss,cmd | grep bash
```

**Expected Output**:
```
user 2345 2345 Ss bash
Threads: 1

Threads: 1

PID VSZ RSS CMD
2345 4234 2340 bash
2346 4234 1560 bash
```

**Explanation**:
- Each bash is separate process
- Each has own memory (VSZ virtual, RSS resident)
- Threads within same process share memory
- Process creation is heavier than thread creation

---

### Solution 4: Observe Lock Blocking

**Script**:
```bash
LOCK="/tmp/test.lock"

# Terminal 1: Hold lock for 5 seconds
hold_lock() {
  echo "Acquiring lock..."
  flock -x 200
  echo "Got lock at $(date +%s)"
  sleep 5
  echo "Releasing lock at $(date +%s)"
}

# Open lock and keep it
exec 200>"$LOCK"
hold_lock

# Terminal 2: Try to acquire (in another terminal)
# flock -x 200 >> /tmp/wait.log
# This will block until lock is released
```

**Explanation**:
- `flock -x 200` = exclusive lock (X lock)
- Second process blocks waiting for lock
- As soon as first releases, second acquires
- Time delta shows waiting duration

---

### Solution 5: Demonstrate Critical Section

**Without Lock** (inconsistent):
```bash
COUNT="/tmp/count.txt"
echo "0" > $COUNT

bad_increment() {
  for i in {1..10}; do
    val=$(cat $COUNT)
    val=$((val + 1))
    echo $val > $COUNT
  done
}

# Run 5 concurrent (result varies: 10-50)
for i in {1..5}; do bad_increment & done
wait
echo "Without lock: $(cat $COUNT)"
```

**With Lock** (consistent):
```bash
good_increment() {
  for i in {1..10}; do
    flock -x 200
    val=$(cat $COUNT)
    val=$((val + 1))
    echo $val > $COUNT
    flock -u 200
  done
}

echo "0" > $COUNT
for i in {1..5}; do good_increment & done
wait
echo "With lock: $(cat $COUNT)"
```

**Expected Output**:
```
Without lock: 23      # Varies each run
With lock: 50         # Always consistent
```

---

## Medium Solutions (6-10)

### Solution 6: Deadlock Scenario

**Script**:
```bash
cat > /tmp/deadlock.sh << 'EOF'
#!/bin/bash
if [ "$1" = "A" ]; then
  exec 201>/tmp/lock1
  echo "A: acquiring lock1"
  flock -x 201
  sleep 2
  echo "A: trying lock2..."
  exec 202>/tmp/lock2
  flock -x 202  # Waits forever - B has it
  echo "A: got lock2"
else
  exec 202>/tmp/lock2
  echo "B: acquiring lock2"
  flock -x 202
  sleep 2
  echo "B: trying lock1..."
  exec 201>/tmp/lock1
  flock -x 201  # Waits forever - A has it
  echo "B: got lock1"
fi
EOF

bash /tmp/deadlock.sh A &
bash /tmp/deadlock.sh B &
# Both hang (deadlock!)
```

**Explanation**:
- Process A: lock1 → wait for lock2 (held by B)
- Process B: lock2 → wait for lock1 (held by A)
- Circular dependency = deadlock
- Neither can progress

---

### Solution 7: Lock Ordering Prevention

**Fixed Script**:
```bash
cat > /tmp/safe.sh << 'EOF'
#!/bin/bash
# Both always acquire lock1 THEN lock2
exec 201>/tmp/lock1
exec 202>/tmp/lock2

echo "Process $1: acquiring lock1"
flock -x 201
echo "Process $1: acquired lock1"
sleep 1

echo "Process $1: acquiring lock2"
flock -x 202
echo "Process $1: acquired lock2"
sleep 1

echo "Process $1: releasing locks"
flock -u 202
flock -u 201
EOF

bash /tmp/safe.sh A &
bash /tmp/safe.sh B &
wait
# Both complete without hang
```

**Explanation**:
- Consistent ordering prevents cycles
- Both processes acquire lock1 first
- One gets lock1, waits for lock2, other waiting for lock1
- When first releases lock1, second can proceed
- No circular wait = no deadlock

---

### Solution 8: Semaphore Simulation

**Script**:
```bash
SEMAPHORE="/tmp/sem.count"
echo "3" > $SEMAPHORE

acquire() {
  while true; do
    exec 200>"$SEMAPHORE.lock"
    flock -x 200
    count=$(cat $SEMAPHORE)
    if [ $count -gt 0 ]; then
      echo $((count - 1)) > $SEMAPHORE
      break
    fi
    flock -u 200
    sleep 0.1
  done
}

release() {
  exec 200>"$SEMAPHORE.lock"
  flock -x 200
  count=$(cat $SEMAPHORE)
  echo $((count + 1)) > $SEMAPHORE
  flock -u 200
}

worker() {
  echo "Worker $1: waiting..."
  acquire
  echo "Worker $1: got resource"
  sleep 2
  echo "Worker $1: releasing"
  release
}

for i in {1..5}; do
  worker $i &
done
wait
```

**Explanation**:
- Semaphore = counter of available resources
- Acquire: decrement when > 0, else wait
- Release: increment
- Only 3 workers run simultaneously

---

### Solution 9: Reader-Writer Problem

**Script**:
```bash
DATA="/tmp/data.txt"
WRITE_LOCK="/tmp/write.lock"

read_data() {
  # Readers don't lock (multiple readers OK)
  echo "Reader $1: reading..."
  sleep 1
  cat $DATA
  echo "Reader $1: done"
}

write_data() {
  # Writer gets exclusive lock
  exec 200>"$WRITE_LOCK"
  echo "Writer: waiting for lock..."
  flock -x 200
  echo "Writer: updating data..."
  echo "Updated at $(date)" >> $DATA
  sleep 2
  flock -u 200
  echo "Writer: done"
}

# Start readers (non-blocking)
read_data 1 &
read_data 2 &

# Writer blocks until readers done (simulated with lock)
write_data &

read_data 3 &
wait
```

**Explanation**:
- Multiple readers can access data simultaneously
- Writer needs exclusive access
- In production, use RWLock (read-write lock)

---

### Solution 10: Measure Lock Contention

**Script**:
```bash
# Without lock
time bash -c '
count=0
for i in {1..1000}; do
  count=$((count + 1))
done
'

# With lock
COUNTER="/tmp/cnt.txt"
echo "0" > $COUNTER

time bash -c '
for i in {1..1000}; do
  flock -x 200 -c "
    val=\$(cat '$COUNTER')
    echo \$((val + 1)) > '$COUNTER'
  "
done
' 200>"$COUNTER.lock"
```

**Expected Output**:
```
real  0m0.003s  # Without lock
real  0m0.250s  # With lock (lock overhead ~80x!)
```

**Explanation**:
- Lock overhead is significant
- Minimize lock time in production
- Batch operations to reduce lock calls
- RWLock better for read-heavy workloads

---

## Practice Tips

1. Always test race conditions multiple times
2. Use timestamps: `date +%s%N` (nanosecond precision)
3. Monitor lock files: `lsof | grep lock`
4. Deadlock: use `timeout 5 cmd` to auto-kill
5. Remember: always acquire locks in same order
