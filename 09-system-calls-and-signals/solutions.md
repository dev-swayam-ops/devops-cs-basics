# 09 - System Calls and Signals Solutions

## Easy Solutions (1-5)

### Solution 1: List All Signals

**Command**:
```bash
kill -l
```

**Output Example**:
```
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
...
```

**Analysis**:
- Total: 64 signals (varies by OS)
- SIGTERM: 15 (graceful)
- SIGKILL: 9 (forced)
- SIGSTOP: 19 (pause)
- By name or number: both work

---

### Solution 2: Send SIGTERM to Process

**Commands**:
```bash
sleep 100 &
PID=$!
kill -TERM $PID
```

**Output Example**:
```
[1] 12345
[1]+  Terminated              sleep 100
```

**Analysis**:
- Signal sent: graceful termination
- Time: immediate (app decides exit)
- Clean shutdown: allows cleanup

---

### Solution 3: Force Kill with SIGKILL

**Commands**:
```bash
sleep 100 &
kill -9 $!
```

**Output Example**:
```
[1] 12346
[1]+  Killed                  sleep 100
```

**Differences**:
- SIGTERM: process can ignore/handle
- SIGKILL: forced (can't catch)
- SIGKILL: process terminates immediately
- SIGKILL: can't clean up

---

### Solution 4: Pause and Resume

**Commands**:
```bash
sleep 100 &
PID=$!
kill -STOP $PID
sleep 2
ps aux | grep $PID  # Check state
kill -CONT $PID
```

**Output Example**:
```
[1] 12347
user  12347  0.0  0.0 ... T    (State: T = stopped)
[1]+  Stopped                  sleep 100
[1]+  Continued                sleep 100
```

**States**:
- S: sleeping (interruptible)
- T: stopped (SIGSTOP)
- R: running

---

### Solution 5: Signal Multiple Processes

**Commands**:
```bash
sleep 100 & sleep 100 & sleep 100 &
pkill -TERM sleep
jobs
```

**Output Example**:
```
[1] 12348
[2] 12349
[3] 12350
[1]-  Terminated    sleep 100
[2]-  Terminated    sleep 100
[3]-  Terminated    sleep 100
```

---

## Medium Solutions (6-10)

### Solution 6: Trap Signal in Bash

**Script**:
```bash
bash -c 'trap "echo Caught SIGTERM!" TERM; sleep 30' &
sleep 1
kill -TERM $!
```

**Output Example**:
```
Caught SIGTERM!
[1]+  Terminated
```

**Explanation**:
- trap catches signal
- Handler executes (echo)
- Process exits cleanly

---

### Solution 7: Ignore Signal with Trap

**Script**:
```bash
bash -c 'trap "" TERM; sleep 30' &
PID=$!
sleep 1
kill -TERM $PID
sleep 2
ps aux | grep $PID
```

**Output Example**:
```
[1] 12352
(process still running after kill)
user 12352 0.0 0.0 ... sleep 30
```

**Note**:
- `trap ""` ignores signal
- Works for signals that can be caught
- SIGKILL (-9) still kills

---

### Solution 8: Identify Zombie Process

**Script**:
```bash
python3 -c "import os; os.fork(); os.exit()" &
sleep 1
ps aux | grep defunct
```

**Output Example**:
```
user  12354  0.0  0.0 ... <defunct>
```

**Explanation**:
- Child exits, parent doesn't reap
- Shows as `<defunct>` in ps
- Parent must call wait()

---

### Solution 9: Send Signal by Name

**Commands**:
```bash
sleep 100 &
PID=$!

# By name
kill -SIGTERM $PID

# By number
sleep 100 &
kill -15 $PID
```

**Result**:
- Same effect (signal 15 = SIGTERM)
- Names: clearer, harder to type
- Numbers: shorter, need reference

---

### Solution 10: Handle SIGINT in Script

**Script**:
```bash
bash -c 'trap "echo Interrupted! Cleaning up..." INT; sleep 30'
```

**On Ctrl+C**:
```
^CInterrupted! Cleaning up...
(script exits)
```

**Analysis**:
- INT = SIGINT = signal 2
- Ctrl+C sends SIGINT
- Trap catches it before exit
- Useful for cleanup tasks

---

## Quick Reference

```bash
# Send signals
kill -TERM $PID          # Graceful
kill -9 $PID             # Force kill
kill -SIGTERM $PID       # By name
pkill -TERM sleep        # Multiple

# Trap in bash
trap "command" SIGNAL
trap "echo done" EXIT
trap "" TERM             # Ignore

# Check signals
kill -l
ps aux                   # See state (T = stopped)
```
