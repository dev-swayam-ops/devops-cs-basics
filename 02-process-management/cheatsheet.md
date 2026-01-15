# 02 - Process Management Cheatsheet

## Starting and Stopping Processes

| Command | Purpose | Example |
|---------|---------|---------|
| `command &` | Start process in background | `sleep 100 &` |
| `Ctrl+Z` | Suspend foreground process | Suspends current process |
| `bg %1` | Resume suspended job in background | Job 1 continues in background |
| `fg %1` | Bring background job to foreground | Job 1 becomes foreground |
| `jobs -l` | List background jobs with PIDs | Shows: [1]+ 2345 Running sleep 100 |

## Sending Signals

| Command | Purpose | Example |
|---------|---------|---------|
| `kill -TERM [PID]` | Graceful termination (default) | `kill -TERM 2345` |
| `kill -9 [PID]` | Force kill (SIGKILL) | `kill -9 2345` |
| `kill -STOP [PID]` | Suspend process | `kill -STOP 2345` |
| `kill -CONT [PID]` | Resume process | `kill -CONT 2345` |
| `kill -0 [PID]` | Test if process exists | Returns 0 if exists |

## Finding Processes

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | List all processes | Shows all with details |
| `ps -ef` | Alternative format | Same as ps aux |
| `ps -fp [PID]` | Show specific PID with details | `ps -fp 2345` |
| `pgrep [name]` | Find PIDs by name | `pgrep sleep` returns 2345 |
| `pidof [name]` | Find PID of program | `pidof bash` |
| `pkill [name]` | Kill by name | `pkill sleep` kills all sleep processes |
| `killall [name]` | Kill all by name | `killall sleep` |

## Process Hierarchy and Details

| Command | Purpose | Example |
|---------|---------|---------|
| `ps -o pid,ppid,user,cmd` | Custom columns | Shows parent-child |
| `pstree -p` | Process tree with PIDs | Visual hierarchy |
| `pstree -p [PID]` | Show subtree | `pstree -p 2345` |
| `ps -o pid,stat,cmd` | Show process state | State: R, S, T, Z, D |
| `ps -o pid,vsz,rss,cmd` | Show memory usage | Virtual and resident memory |

## Job Control

| Command | Purpose | Example |
|---------|---------|---------|
| `jobs` | List current jobs | Shows: [1]+ Running |
| `jobs -l` | List with PIDs | Shows: [1]+ 2345 Running |
| `bg %1` | Resume in background | Continue suspended job |
| `fg %1` | Bring to foreground | Make background job active |
| `wait [PID]` | Wait for process to finish | Blocks until done |

## Process States Quick Reference

| State | Meaning | Symbol |
|-------|---------|--------|
| Running | Executing or ready to execute | R |
| Sleeping | Waiting for event (normal) | S |
| Disk Sleep | Uninterruptible wait | D |
| Stopped | Suspended (SIGSTOP) | T |
| Traced | Under debugger | t |
| Zombie | Exited, parent not cleaned up | Z |

## Signal Numbers Quick Reference

| Number | Name | Effect | Catchable |
|--------|------|--------|-----------|
| 1 | SIGHUP | Hangup | Yes |
| 2 | SIGINT | Interrupt (Ctrl+C) | Yes |
| 9 | SIGKILL | Kill (forced) | No |
| 15 | SIGTERM | Terminate (graceful) | Yes |
| 18 | SIGCONT | Continue (resume) | Yes |
| 19 | SIGSTOP | Stop (suspend) | No |

## Environmental Variables for Processes

| Variable | Meaning | Example |
|----------|---------|---------|
| `$$` | Current shell PID | `echo $$` returns 2345 |
| `$!` | Last background PID | `sleep 100 & echo $!` |
| `$?` | Last command exit code | 0 = success, non-0 = error |
| `$PPID` | Parent process ID | Parent shell's PID |
| `$USER` | Current user | `echo $USER` |

## Advanced Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux --sort=-%cpu` | Sort by CPU usage | Highest CPU first |
| `ps aux --sort=-%mem` | Sort by memory usage | Highest memory first |
| `ps -eo pid,user,cpu,mem,cmd` | Custom columns | Show specific info |
| `watch -n 1 'ps aux'` | Monitor processes live | Refresh every 1 second |
| `top` | Real-time process monitoring | Interactive monitoring |
| `htop` | Better top UI | More user-friendly |

## Process Lifecycle Diagram

```
1. fork()    → New process created (copy of parent)
2. exec()    → Load new program into process
3. Running   → Process executes
4. Signal    → Process receives signal
5. Exit      → Process terminates
6. wait()    → Parent cleans up child
```

## Common Process Scenarios

### Scenario 1: Kill Unresponsive Process
```bash
ps aux | grep processname        # Find process
kill -TERM [PID]                # Try graceful first
kill -9 [PID]                   # Force if needed
```

### Scenario 2: Monitor Background Job
```bash
command &                        # Start in background
jobs -l                         # Monitor jobs
fg %1                          # Check if still running
```

### Scenario 3: Create Zombie (for testing)
```bash
bash -c 'sleep 1' &            # Parent exits immediately
ps -o ppid,stat,cmd | grep 1   # Child becomes zombie
```

### Scenario 4: Suspend and Resume
```bash
sleep 1000                      # Start foreground
# Type Ctrl+Z
bg                             # Resume in background
fg %1                          # Back to foreground
```
