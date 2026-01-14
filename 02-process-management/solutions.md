# 02 - Process Management: Solutions

## Solution 1: Understanding Process IDs

### Commands

```bash
echo $$        # Your shell's PID
ps -p $$       # Your shell's process details
echo $PPID     # Your shell's parent PID
```

### Expected Output

```
$$
12345

ps -p $$
PID TTY      STAT   TIME COMMAND
12345 pts/0  Ss     0:00 bash

$PPID
1234
```

### Answers

1. **Your shell's PID**: The number printed by `echo $$` (e.g., 12345)
2. **Your shell's parent PID**: From `$PPID` or ps output PPID column
3. **Parent process**: Use `ps -p $PPID` to see what process started your shell (usually `sshd` or `systemd`)

---

## Solution 2: Process Hierarchy

### Command

```bash
pstree -p
```

### Example Output

```
systemd(1)
├─systemd-journald(235)
├─systemd-udevd(255)
├─sshd(456)
│ └─sshd(1234)
│   └─bash(12345)  ← Your shell
│     └─pstree(12500)
└─systemd-logind(300)
```

### Answers

1. **systemd (PID 1)**: Parent of most system processes
2. **Child processes**: systemd-journald, systemd-udevd, sshd, systemd-logind
3. **Your shell**: bash with PID shown in pstree output

---

## Solution 3: Finding Processes by Name

### Commands

```bash
ps aux | grep bash
pgrep bash
pgrep -a bash    # Show command too
```

### Output Comparison

```
ps aux | grep bash
root    456  0.0  0.1  21456  2048 ?  Ss  10:00  0:01 sshd
user   1234  0.0  0.3  29000  7000 pts/0  Ss  10:15  0:00 bash
user   2000  0.0  0.1  25000  2048 pts/1  S   11:00  0:00 grep bash

pgrep bash
1234
2000  ← This is the grep command itself!
```

### Answers

1. **Bash process count**: 1 (PID 1234) - grep bash also matches but it's grep, not bash
2. **Your shell PID**: Compare with `echo $$`
3. **Difference**: `ps | grep` shows full lines and includes grep itself; `pgrep` shows just PIDs

### Better Approach

```bash
pgrep -f "^bash"  # Only exact bash, not grep
ps aux | grep bash | grep -v grep
```

---

## Solution 4: Process States

### Commands

```bash
sleep 100 &
sleep 200 &
sleep 300 &
sleep 400 &
jobs -l
ps aux | grep sleep
```

### Output

```
jobs -l
[1]  1234 Running    sleep 100 &
[2]  1235 Running    sleep 200 &
[3]  1236 Running    sleep 300 &
[4]  1237 Running    sleep 400 &

ps aux | grep sleep
user  1234  0.0  0.0  4512  256  pts/0  S  10:00  0:00 sleep 100
user  1235  0.0  0.0  4512  256  pts/0  S  10:00  0:00 sleep 200
user  1236  0.0  0.0  4512  256  pts/0  S  10:00  0:00 sleep 300
user  1237  0.0  0.0  4512  256  pts/0  S  10:00  0:00 sleep 400
```

### Answers

1. **All sleep processes**: Running (sleeping waiting for timer)
2. **State**: `S` = Sleeping/Interruptible
3. **Difference**: `jobs` shows shell job status; `ps aux` shows all system processes
4. **State meaning**: These processes are blocked waiting for timer interrupt

---

## Solution 5: Signals and Termination

### Commands

```bash
sleep 60 &
kill -l
kill -TERM $$    # Send SIGTERM to yourself (careful!)
kill -KILL $$    # Send SIGKILL (usually fatal!)
```

### Key Signals

| Signal | Number | Meaning | Catchable |
|--------|--------|---------|-----------|
| SIGHUP | 1 | Hangup (terminal closed) | Yes |
| SIGINT | 2 | Interrupt (Ctrl+C) | Yes |
| SIGQUIT | 3 | Quit (Ctrl+\\) | Yes |
| SIGTERM | 15 | Terminate (graceful) | Yes |
| SIGKILL | 9 | Kill (forced) | **No** |

### Answers

1. **SIGTERM vs SIGKILL**: 
   - SIGTERM: Graceful termination, process can clean up, can be caught
   - SIGKILL: Forced termination, process cannot catch it, immediate death
2. **When to use each**:
   - SIGTERM first (give process time to clean up)
   - SIGKILL only if SIGTERM doesn't work
3. **Default signal**: `kill` without flags sends SIGTERM (-15)

---

## Solution 6: Background and Foreground Jobs

### Commands

```bash
sleep 100
# Ctrl+Z pressed → [1]+ Stopped  sleep 100

bg          # Resume in background
# [1]+ sleep 100 &

fg %1       # Bring to foreground
# sleep 100

# Ctrl+Z pressed again
kill %1     # Kill job 1
# [1]-  Terminated  sleep 100
```

### Practical Example

```bash
# Start 3 jobs
sleep 200 &  # [1]
sleep 300 &  # [2]
sleep 400 &  # [3]

# List them
jobs
# [1]- Running  sleep 200 &
# [2]+ Running  sleep 300 &
# [3]  Running  sleep 400 &

# Bring job 2 to foreground
fg %2
# sleep 300
# (Ctrl+Z to suspend)

# Background it
bg
# [2]+ sleep 300 &

# Kill job 3
kill %3
# [3]-  Terminated  sleep 400
```

### Answers

1. **Ctrl+Z**: Suspends (pauses) the process, returns control to shell
2. **bg vs fg**: `bg` = resume suspended process in background; `fg` = resume in foreground
3. **Kill by job**: `kill %1`, `kill %2`, etc. (% = job number, not PID)
4. **Multiple jobs**: Use `jobs` to list, `fg %2` to switch, `bg` to background

---

## Solution 7: Process Resource Usage

### Commands

```bash
python3 -c "import time; x = [0]*10000000; time.sleep(100)" &
ps aux --sort=-%mem | head -5
top -n 1 -p [PID]
cat /proc/[PID]/status | grep VmRSS
```

### Output Example

```
ps aux --sort=-%mem
USER    PID %CPU %MEM    VSZ    RSS COMMAND
python3 1234  0.0  5.2  345600 401256 python3 -c...

top -n 1 -p 1234
PID  USER  PR NI  VIRT   RES  SHR S %CPU %MEM
1234 user  20 0  345M  392M  1.2M S  0.0  5.2
```

### Answers

1. **Most memory**: Python process using ~5.2% of system RAM
2. **CPU usage**: 0.0% (process is sleeping in `time.sleep()`)
3. **VSZ (Virtual Memory)**: 345M (memory process could use)
4. **Difference**:
   - **VSZ**: Total virtual memory allocated (includes swapped pages, libraries)
   - **RSS**: Resident Set Size (actual physical memory in RAM)
   - **Rule**: RSS is more relevant for actual memory usage

---

## Solution 8: Zombie Processes

### Commands

```bash
# Create zombie
bash -c 'bash -c "echo test; exit 0" &' &
sleep 1
ps aux | grep defunct
```

### Cleanup

```bash
# Find and kill parent
ps aux | grep defunct
kill -9 [PARENT_PID]
ps aux | grep defunct  # Should be gone
```

### Answers

1. **Zombie process**: Process that has exited but parent hasn't reaped its exit status
2. **Why they exist**: Parent must call `wait()` to read child exit status
3. **Prevention**: Parent should handle SIGCHLD or use `wait()`
4. **Cleanup**: Kill parent process; parent must reap child or reparent to init

### Example Prevention Code

```bash
# Good: Script waits for children
#!/bin/bash
background_job() {
    sleep 100 &
    wait $!  # Wait for child to finish
}
background_job
```

---

## Solution 9: Process Communication via Signals

### Script Creation

```bash
cat > test_signal.sh << 'EOF'
#!/bin/bash
trap 'echo "Received SIGTERM"; exit 0' TERM
trap 'echo "Received SIGINT"; exit 1' INT
trap 'echo "Received SIGUSR1"' USR1

echo "PID: $$"
while true; do
    echo "Running..."
    sleep 1
done
EOF

chmod +x test_signal.sh
./test_signal.sh &
PID=$!

sleep 1
kill -TERM $PID
```

### Answers

1. **trap**: Registers a handler for a signal; when signal received, handler runs
2. **Signal handling**: Process can respond to signals with custom code
3. **SIGKILL**: Cannot trap SIGKILL - it's designed to force termination
4. **SIGUSR1 handler**: Added in script above; can be used for custom purposes

---

## Solution 10: Process Accounting and Audit

### Commands

```bash
# View process info
cat /proc/[PID]/status
cat /proc/[PID]/cmdline
cat /proc/[PID]/io
cat /proc/[PID]/limits

# Monitor new processes
sudo auditctl -a always,exit -F arch=b64 -S execve

# Who is logged in
w
whoami
```

### Example Output

```
cat /proc/1234/status
Name:   bash
State:  S (sleeping)
Pid:    1234
PPid:   1200
Uid:    1000   1000   1000   1000
Gid:    1000   1000   1000   1000
VmPeak:  29000 kB
VmSize:  29000 kB
VmRSS:    7000 kB
```

### Answers

1. **`/proc/[PID]/`** contains: status, cmdline, io, limits, maps, fd (file descriptors), net
2. **DevOps use cases**: 
   - Detect unauthorized processes
   - Monitor resource usage
   - Track parent-child relationships
   - Audit process execution history
3. **Monitoring script**:
   ```bash
   watch -n 1 'ps aux | sort -k3 -r | head -10'
   ```
4. **Unauthorized detection**:
   ```bash
   # Alert on non-standard processes
   ps aux | grep -v systemd | grep -v sshd | wc -l
   ```

---
