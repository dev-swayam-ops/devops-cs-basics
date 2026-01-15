# 02 - Process Management Solutions

## Easy Solutions (1-5)

### Solution 1: Start and Monitor Background Process

**Commands**:
```bash
# Start background process
sleep 3600 &

# List background jobs
jobs -l

# Get detailed info
ps -fp 2345  # Replace 2345 with actual PID
```

**Output Example**:
```
[1] 2345
[1]+ 2345 Running   sleep 3600 &
```

**Explanation**:
- `&` puts process in background
- `[1]` = job number (used with `fg` and `bg`)
- `2345` = PID (used with `kill`)
- `Running` = process state
- Process runs independently, shell returns immediately

---

### Solution 2: Send Signals to Process

**Commands**:
```bash
# Start background process
sleep 3600 &
PID=$!  # Get last background PID

# Send SIGTERM (graceful)
kill -TERM $PID

# Send SIGKILL (forced) - only if TERM doesn't work
kill -KILL $PID
```

**Output Example**:
```
[1]- Terminated   sleep 3600
```

**Explanation**:
- SIGTERM (15) = polite termination request
- SIGKILL (9) = forced termination, cannot be caught
- Process exits and shell reports termination
- Always try SIGTERM first, SIGKILL as last resort

---

### Solution 3: Find Processes by Name

**Commands**:
```bash
# Start multiple sleep processes
sleep 100 &
sleep 200 &
sleep 300 &

# Find by name
pgrep sleep
pidof sleep

# Kill all
killall sleep
```

**Output Example**:
```
2345
2346
2347
```

**Explanation**:
- `pgrep` returns PIDs matching pattern
- `pidof` finds PIDs by exact program name
- `killall` kills all processes with given name
- More convenient than manual kill for multiple processes

---

### Solution 4: Check Process Parent-Child Relationship

**Commands**:
```bash
# Get current shell PID
echo $$

# Show hierarchy
ps -o pid,ppid,user,cmd

# Start background process
bash &

# Show again with child
ps -o pid,ppid,cmd
```

**Output Example**:
```
2000  # Your shell PID

PID  PPID USER  CMD
2000 1999 user  -bash
2345 2000 user  bash     # Child of shell 2000
```

**Explanation**:
- Your shell (PID 2000) parent is terminal (PID 1999)
- Background bash (PID 2345) parent is your shell (PPID 2000)
- Parent-child chain: Terminal → Shell → Background bash

---

### Solution 5: View Process Details

**Commands**:
```bash
# View process with full details
ps -fp 1

# Show specific columns
ps -o pid,user,vsz,rss,stat,cmd -p $$

# Detailed stat
stat /proc/2345
```

**Output Example**:
```
UID  PID  PPID  C STIME TTY    STAT TIME+ COMMAND
root 1    0     0 10:00 ?      Ss   0:01 /sbin/init
```

**Explanation**:
- `PID` = Process ID
- `PPID` = Parent Process ID
- `VSZ` = Virtual memory size (allocated, KB)
- `RSS` = Resident memory (actually in RAM, KB)
- `STAT` = Process state (R=running, S=sleeping, T=stopped, Z=zombie)
- `C` = CPU (always 0 in ps, use top for real CPU %)

---

## Medium Solutions (6-10)

### Solution 6: Create Parent-Child Process Chain

**Commands**:
```bash
# Open shell and note PID
echo "Shell 1: $$"

# In this shell, start background bash
bash &

# In that bash, start another bash
# (Type command in the background bash)
bash &

# Show hierarchy
pstree -p
```

**Output Example**:
```
bash(2000)
  ├─bash(2345)
  │   └─bash(2346)
  └─sleep(2347)
```

**Explanation**:
- Each bash process has parent as the previous bash
- Can nest multiple levels (limited by ulimit)
- `pstree` shows entire hierarchy at once
- Each level consumes memory and process slots

---

### Solution 7: Monitor Process State Changes

**Commands**:
```bash
# Start process
sleep 100 &
PID=$!

# Check initial state
ps -o pid,stat,cmd | grep $PID

# Suspend with Ctrl+Z
# (Type in shell)

# Check state (should be T for stopped)
ps -o pid,stat,cmd | grep $PID

# Resume in background
bg

# Check state (should be R or S)
ps -o pid,stat,cmd | grep $PID

# Suspend again with kill
kill -STOP $PID

# Resume with kill
kill -CONT $PID
```

**Output Example**:
```
2345 S   sleep 100      # Running
2345 T   sleep 100      # Stopped (suspended)
2345 S   sleep 100      # Running again
```

**Explanation**:
- `R` = Running (on CPU or ready queue)
- `S` = Sleeping (waiting for event)
- `T` = Stopped/Traced (suspended)
- Ctrl+Z sends SIGSTOP, `bg` sends SIGCONT
- `kill -STOP` and `kill -CONT` also control suspension

---

### Solution 8: Handle Zombie Process

**Commands**:
```bash
# Create zombie
bash -c 'sleep 1' &
PARENT_PID=$!

# Immediately check
ps -o pid,ppid,stat,cmd | grep sleep

# Wait a moment and check again
sleep 2
ps -o pid,ppid,stat,cmd | grep sleep
```

**Output Example**:
```
2346 2345 S   sleep 1      # Normal, parent 2345 is bash
2346 1    Z   sleep 1      # Zombie, parent 1 is init
# (After child exits completely)
# (No output - process cleaned up)
```

**Explanation**:
- Zombie created when child exits but parent doesn't wait()
- PPID changes to 1 (init) = parent was terminated
- Init periodically cleans up zombies
- In real code, use wait() to prevent zombies

---

### Solution 9: Control Foreground and Background

**Commands**:
```bash
# Start foreground process
sleep 100

# (Type Ctrl+Z to suspend)
# Shell shows: [1]+ Stopped   sleep 100

# List jobs
jobs -l

# Resume in background
bg %1

# List again (should be Running)
jobs -l

# Bring to foreground
fg %1

# Suspend again
# (Type Ctrl+Z)

# Continue switching
bg %1
fg %1
```

**Output Example**:
```
[1]+ 2345 Stopped   sleep 100      # After Ctrl+Z
[1]+ 2345 Running   sleep 100      # After bg
[1]+ 2345 Stopped   sleep 100      # After Ctrl+Z again
```

**Explanation**:
- Ctrl+Z suspends foreground process
- `bg` resumes in background (sends SIGCONT)
- `fg` brings to foreground
- Can switch unlimited times
- Process maintains state across fg/bg changes

---

### Solution 10: Signal Handler Demonstration

**Commands**:
```bash
# Test SIGTERM (graceful)
sleep 3600 &
PID1=$!
kill -TERM $PID1        # Process exits gracefully

# Test SIGSTOP (suspend)
sleep 3600 &
PID2=$!
kill -STOP $PID2        # Process suspended
ps -o pid,stat,cmd | grep $PID2  # State shows T

# Resume
kill -CONT $PID2        # Process resumes
ps -o pid,stat,cmd | grep $PID2  # State shows S

# Test SIGKILL (forced)
sleep 3600 &
PID3=$!
kill -KILL $PID3        # Immediately terminates
```

**Output Example**:
```
[1]- Terminated   sleep 3600   # SIGTERM result
2347 T   sleep 3600             # SIGSTOP result
2347 S   sleep 3600             # SIGCONT result
[2]- Killed      sleep 3600   # SIGKILL result
```

**Explanation**:
- SIGTERM (15) = default kill, graceful exit
- SIGSTOP (19) = suspend, cannot be caught
- SIGCONT (18) = resume, opposite of STOP
- SIGKILL (9) = forceful termination, cannot be caught
- SIGTERM > SIGSTOP/CONT > SIGKILL (order of preference)

---

## Tips for Practice

1. Always note PID and job number when starting processes
2. Use `jobs` to monitor background processes
3. Compare `kill -TERM` vs `kill -KILL` behavior
4. Understand state transitions: R → T → S → exit
5. Practice job control: Ctrl+Z, bg, fg, jobs
