# 03 - Threads and Concurrency Cheatsheet

## Thread and Process Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps -eLf` | List processes with thread info | Shows LWP (Light Weight Process ID) |
| `ps -eLf \| grep bash` | Filter processes by name | Find all bash threads |
| `grep Threads /proc/[pid]/status` | Thread count of process | Returns: Threads: 4 |
| `pstree -p [pid]` | Show process tree | Visual parent-child hierarchy |
| `top -H` | Show threads (top -H) | Interactive thread monitoring |

## File Locking Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `flock -x 200` | Exclusive lock (X) | Only one process at a time |
| `flock -s 200` | Shared lock (S) | Multiple readers, no writers |
| `flock -u 200` | Unlock | Release lock on file descriptor |
| `flock 200 < file` | Lock file until command ends | Implicit unlock on exit |
| `flock -n 200` | Non-blocking lock | Return error if locked |

## Lock and Synchronization

| Concept | Implementation | Purpose |
|---------|----------------|---------|
| Mutex | flock (exclusive) | Binary lock (locked/unlocked) |
| Semaphore | Counter + lock | N concurrent accesses |
| Lock Ordering | Always acquire same order | Prevent deadlock |
| Critical Section | Code within lock block | Shared data access |
| Timeout | flock -w N | Don't wait forever |

## Process States in Concurrency

| State | Meaning | Symbol |
|-------|---------|--------|
| Running | Executing on CPU | R |
| Sleeping | Waiting (not on CPU) | S |
| Waiting for Lock | Blocked on lock | S (sleepable) |
| Uninterruptible | I/O wait, can't signal | D |
| Stopped | SIGSTOP received | T |

## Common Race Condition Patterns

| Pattern | Risk | Solution |
|---------|------|----------|
| Increment counter | Lost updates | Lock before modify |
| Read-modify-write | Dirty read | Atomic operation or lock |
| Check-then-act | TOCTOU bug | Lock entire operation |
| Double-check lock | Invalid cache | Careful synchronization |

## Deadlock Detection and Prevention

| Technique | How | Example |
|-----------|-----|---------|
| Lock Ordering | Always same order | A→B→C, never B→A |
| Timeout | Abandon after wait | flock -w 10 (10s timeout) |
| Resource Limit | Prevent allocation | ulimit -n (file descriptors) |
| Banker's Algorithm | Pre-allocate resources | Ensure no circular wait possible |

## Performance Metrics

| Metric | Measurement | Tools |
|--------|-------------|-------|
| Lock Time | Time lock held | Timestamp before/after |
| Contention | Processes waiting locks | Monitor with ps |
| Throughput | Operations/second | Time N iterations |
| Latency | Time per operation | time command |

## Bash Synchronization Patterns

### Pattern 1: Simple Lock
```bash
exec 200>"/tmp/my.lock"
flock -x 200
# Critical section here
flock -u 200
exec 200>&-
```

### Pattern 2: Semaphore
```bash
exec 200>"/tmp/sem.lock"
while ! flock -n 200; do
  sleep 0.1
done
# Got lock (resource acquired)
```

### Pattern 3: Lock with Cleanup
```bash
lock="/tmp/my.lock"
exec 200>"$lock"
trap "flock -u 200; exit" EXIT
flock -x 200
# Critical section (auto-unlocks on exit)
```

### Pattern 4: Read-Write Lock
```bash
# Readers (non-exclusive)
flock -s 200  # Shared lock
# Multiple readers can hold

# Writer (exclusive)
flock -x 200  # Exclusive lock
# Only one writer
```

## Common Issues and Solutions

| Issue | Cause | Fix |
|-------|-------|-----|
| Deadlock | Circular lock dependencies | Use lock ordering |
| Race condition | Unsynchronized access | Add synchronization |
| Starvation | Low-priority tasks never run | Fair scheduling |
| Busy-wait | Loop without sleep | Add sleep delay |
| Lock overhead | Too many lock acquisitions | Batch operations |

## Thread Safety Checklist

- ✓ All shared data protected by locks?
- ✓ Locks acquired in consistent order?
- ✓ Locks released in all code paths?
- ✓ No lock nesting (or deadlock-safe)?
- ✓ Deadlock timeout set?
- ✓ Exception handling releases locks?
- ✓ Shared state documented?
- ✓ Performance acceptable?

## Commands for Debugging Concurrency

| Command | Purpose | Example |
|---------|---------|---------|
| `lsof /tmp/` | See open files/locks | Shows lock files |
| `fuser [file]` | Find process using file | `fuser /tmp/my.lock` |
| `strace -e trace=open` | Trace syscalls | See lock operations |
| `timeout 5 cmd` | Kill hung process | `timeout 5 ./deadlock` |
| `ps -o pid,state,cmd` | Show process states | See which waiting |
