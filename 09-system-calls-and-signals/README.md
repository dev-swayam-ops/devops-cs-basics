# 09 - System Calls and Signals

## What You'll Learn

- System calls overview (fork, exec, exit, read, write)
- Signals and signal types
- Signal handling and trapping
- Kill command and signal delivery
- Process termination
- Zombie processes
- Signal safety

## Prerequisites

- Completed **08-linux-kernel-basics**
- Understanding of processes
- Basic bash scripting

## Key Concepts

### 1. Common System Calls
- **fork**: Create child process (copy of parent)
- **exec**: Replace process with new program
- **exit**: Terminate process
- **read**: Read from file/socket
- **write**: Write to file/socket
- **open/close**: File operations

### 2. Signal Types
```
SIGTERM (15): Graceful termination (default)
SIGKILL (9):  Forced kill (can't catch)
SIGSTOP (19): Pause process (can't catch)
SIGCONT (18): Resume process
SIGHUP (1):   Hangup (terminal closed)
SIGINT (2):   Interrupt (Ctrl+C)
SIGQUIT (3):  Quit (Ctrl+\)
SIGUSR1 (10): User-defined
SIGUSR2 (12): User-defined
SIGCHLD (17): Child terminated
```

### 3. Signal Flow
```
Signal sent → Delivered to process → Handler executed → Process resumes
(or default action)
```

### 4. Zombie Processes
- Child finishes, parent hasn't reaped (read exit status)
- Stays in process table until parent calls wait()
- Not dangerous, but wastes PID

### 5. Signal Safety
- Some functions unsafe in signal handlers
- Use async-signal-safe functions only

## Hands-on Lab: Signals and Process Control

### Lab Overview
Send signals, handle them, observe process behavior.

### Lab Commands

```bash
# 1. List all signals
kill -l

# Expected: (all 64 signals listed)

# 2. Start long-running process
sleep 1000 &

# Expected: [1] 12345

# 3. Send SIGTERM (graceful)
kill -TERM %1

# Expected: (process terminates)

# 4. Send SIGKILL (force)
sleep 1000 & sleep 1 && kill -9 %1

# Expected: (killed forcefully)

# 5. Send signal by name
kill -SIGTERM $(pgrep sleep)

# Expected: (process exits)

# 6. Pause and resume process
sleep 1000 & sleep 1 && kill -STOP %1
kill -CONT %1

# Expected: (process paused, then resumes)

# 7. Trap signal in bash
bash -c 'trap "echo Caught SIGTERM" TERM; sleep 10' & 
kill -TERM $!

# Expected: Caught SIGTERM

# 8. Ignore signal
bash -c 'trap "" TERM; sleep 10' &
kill -TERM $!

# Expected: (no output, process continues)

# 9. Check for zombie
sleep 1000 & sleep 1 && kill -9 $!
ps aux | grep defunct

# Expected: (zombie process visible)

# 10. Send signal to all processes
pkill -SIGTERM sleep

# Expected: (all sleep processes terminate)
```

## Validation

```bash
# Can you list signals?
kill -l && echo "✓ Signals listed"

# Send signals?
sleep 5 & kill -TERM $! && echo "✓ Signal sent"

# Trap signals?
bash -c 'trap "echo OK" TERM' && echo "✓ Trap works"
```

## Cleanup

```bash
# Kill any remaining processes
pkill -9 sleep 2>/dev/null || true
```

## Common Mistakes

1. **SIGKILL vs SIGTERM**: SIGKILL forces, SIGTERM asks nicely
2. **Zombie handling**: Need parent to wait() for child
3. **Trap placement**: Must be early in script
4. **Signal numbers**: Different systems may vary
5. **Multiple signals**: Can't queue (only one of each)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Process won't die | Use: kill -9 (SIGKILL) |
| Trap not working | Check: syntax, signal name |
| Zombie process | Parent not calling wait() |
| Signal not received | Check: process group, permissions |
| Process ignores signal | May have trap "" (ignoring) |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **10-scheduling-and-performance** after completion

## Additional Resources

- Signals: `man 7 signal`, `man 2 kill`
- Trap: `man bash` (search "trap")
- Kill: `man kill`, `man pkill`

