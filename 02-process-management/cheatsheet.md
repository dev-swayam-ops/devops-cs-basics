# 02 - Process Management: Cheatsheet

## Essential Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | List all processes with details | `ps aux \| grep bash` |
| `ps -ef` | List processes in tree format | `ps -ef --forest` |
| `ps -p PID` | Show specific process | `ps -p $$` |
| `pstree` | Show process hierarchy | `pstree -p` |
| `pgrep name` | Find PID by process name | `pgrep bash` |
| `pgrep -a name` | Find PID with command line | `pgrep -a bash` |
| `jobs` | List background/suspended jobs | `jobs -l` |
| `bg` | Resume suspended job in background | `bg %1` |
| `fg` | Bring background job to foreground | `fg %1` |
| `kill PID` | Terminate process (SIGTERM) | `kill 1234` |
| `kill -9 PID` | Force kill process (SIGKILL) | `kill -9 1234` |
| `kill -l` | List all available signals | `kill -l` |
| `killall name` | Kill all processes by name | `killall sleep` |
| `top` | Real-time process monitoring | `top` |
| `htop` | Enhanced process viewer | `htop` |
| `nice -n priority cmd` | Run command with priority | `nice -n 10 ./script.sh` |
| `renice priority -p PID` | Change process priority | `renice 5 -p 1234` |
| `nohup cmd` | Run command immune to hangups | `nohup ./long_job.sh &` |
| `disown` | Remove job from shell | `disown %1` |
| `wait` | Wait for process to finish | `wait $!` |
| `trap` | Handle signals in scripts | `trap 'cleanup' EXIT` |
| `cat /proc/[PID]/status` | Process status details | `cat /proc/$$/status` |
| `cat /proc/[PID]/cmdline` | Process command line | `cat /proc/1234/cmdline` |

---

## Process States

```
R (Running)      = Currently executing
S (Sleeping)     = Waiting for I/O or event
D (Disk Sleep)   = Uninterruptible (I/O wait)
Z (Zombie)       = Terminated, parent hasn't reaped
T (Stopped)      = Suspended (Ctrl+Z)
W (Paging)       = Swapped out pages
X (Dead)         = Process is dead
< (High Priority) = Has high priority
N (Low Priority)  = Has low priority
+ (Foreground)    = Running in foreground
l (Multithreaded) = Process has threads
s (Session Leader)= Process is session leader
```

---

## Signals Reference

| Signal | Number | Default Action | Purpose |
|--------|--------|-----------------|---------|
| SIGHUP | 1 | Terminate | Terminal disconnected |
| SIGINT | 2 | Terminate | Ctrl+C |
| SIGQUIT | 3 | Core Dump | Ctrl+\\ |
| SIGILL | 4 | Core Dump | Illegal instruction |
| SIGTRAP | 5 | Core Dump | Debugger trap |
| SIGABRT | 6 | Core Dump | Abort signal |
| SIGBUS | 7 | Core Dump | Bus error |
| SIGFPE | 8 | Core Dump | Floating point error |
| SIGKILL | 9 | **Cannot catch** | Force kill |
| SIGUSR1 | 10 | Terminate | User-defined signal 1 |
| SIGUSR2 | 12 | Terminate | User-defined signal 2 |
| SIGPIPE | 13 | Terminate | Broken pipe |
| SIGALRM | 14 | Terminate | Alarm clock |
| SIGTERM | 15 | Terminate | Graceful termination |
| SIGSTOP | 19 | **Cannot catch** | Stop process |
| SIGCONT | 18 | Continue | Resume process |
| SIGCHLD | 17 | Ignore | Child process ended |
| SIGTSTP | 20 | Stop | Ctrl+Z |
| SIGTTIN | 21 | Stop | Background read |
| SIGTTOU | 22 | Stop | Background write |

---

## Process Creation Flow

```
Program on Disk (Executable)
        ↓
    fork()
        ↓
Child Process (Copy of Parent)
        ↓
    exec()
        ↓
New Program in Memory (Process)
        ↓
Process Running

Example in Shell:
ls -la → shell fork()s → child does exec(/bin/ls)
```

---

## Job Control

```bash
sleep 100
# Ctrl+Z → [1]+ Stopped

jobs -l
# [1]+ 1234 Stopped  sleep 100

bg %1
# [1]+ sleep 100 &

jobs -l
# [1]+ 1234 Running  sleep 100 &

fg %1
# sleep 100
# (Ctrl+Z)

kill %1
# [1]-  Terminated  sleep 100
```

---

## PS Output Columns

```
USER        = Process owner
PID         = Process ID
%CPU        = CPU usage percentage
%MEM        = Memory usage percentage
VSZ         = Virtual memory size (KB)
RSS         = Resident set size (physical memory in KB)
TTY         = Terminal (? = not attached to terminal)
STAT        = Process state
START       = Start time
TIME        = CPU time used
COMMAND     = Command that started process
```

---

## Priority and Niceness

```
Nice Level:  -20 to +19
-20 = Highest priority (most important)
 0 = Default priority
+19 = Lowest priority (least important)

Lower number = Higher priority!

# Run with low priority (nicevalue 10)
nice -n 10 ./my_script.sh

# Change priority of running process
renice -n 5 -p 1234  # Set to nice value 5
renice -n 5 -u john  # All processes of user john
```

---

## Common Process Management Patterns

```bash
# Run in background
command &

# Suppress all output
command > /dev/null 2>&1 &

# Run with no hangup (survives logout)
nohup command &

# Run multiple commands in sequence
(cmd1 && cmd2 && cmd3) &

# Run multiple commands in parallel
(cmd1 &) && (cmd2 &) && (cmd3 &) && wait

# Wait for all background jobs
cmd1 &
cmd2 &
cmd3 &
wait  # Block until all finish

# Get PID of last background job
sleep 100 &
PID=$!
echo $PID
```

---

## Process Debugging

```bash
# Trace system calls
strace ./program

# Trace signals
strace -f ./program  # Follow forks

# Show open files
lsof -p 1234

# Show network connections
ss -pt | grep 1234

# CPU and memory trend
ps aux --sort=-%mem | head -10

# Watch process in real-time
watch -n 1 'ps aux | grep [process_name]'

# Get exit code of last command
echo $?
```

---

## Zombie Process Handling

```
Zombie = Process exited but parent didn't call wait()

Detection:
ps aux | grep defunct
ps aux | grep Z

Cleanup:
kill -9 [PARENT_PID]  # Kill parent

Prevention in Scripts:
#!/bin/bash
background_job &
wait $!  # Wait for child
```

---

## Process Communication (IPC)

```
Pipes:
cmd1 | cmd2          # Output of cmd1 → Input of cmd2

File Descriptors:
0 = stdin
1 = stdout
2 = stderr

Redirection:
cmd > file           # stdout to file
cmd 2> file          # stderr to file
cmd &> file          # stdout and stderr
cmd < file           # stdin from file

Process Substitution:
cmd <(other_cmd)     # Feed output as input
cmd >(other_cmd)     # Send output to command
```

---

## Useful One-liners

```bash
# Kill all processes matching pattern
killall -9 pattern

# Kill all processes owned by user
killall -u username

# Monitor process creation
watch -n 1 'ps aux | wc -l'

# Find memory hogs
ps aux --sort=-%mem | head -5

# Find CPU hogs
ps aux --sort=-%cpu | head -5

# Process count by user
ps aux | awk '{print $1}' | sort | uniq -c

# Kill process on specific port
lsof -ti:[PORT] | xargs kill -9

# Wait for process to finish
wait [PID]

# Graceful shutdown pattern
kill -TERM [PID] && sleep 5 && kill -KILL [PID]
```

---
