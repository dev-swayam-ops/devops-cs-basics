# 02 - Process Management

## What You'll Learn

Master process management concepts:
- Process lifecycle and states
- Process creation with fork() and exec()
- Parent-child relationships
- Process signals and termination
- Process groups and sessions
- Background and foreground processes
- Process monitoring and management

## Prerequisites

- Completed **01-os-fundamentals**
- Understanding of PID and PPID
- Basic Linux commands

## Key Concepts

### 1. Process Lifecycle
- **New**: Just created, not yet running
- **Running**: Executing on CPU
- **Waiting**: Blocked, waiting for resource
- **Stopped**: Suspended by signal
- **Zombie**: Terminated but parent hasn't cleaned up
- **Terminated**: Exited completely

### 2. Process Creation (fork + exec)
- fork() creates child process (copy of parent)
- exec() replaces process memory with new program
- Parent and child continue independently

### 3. Process Signals
- **SIGTERM** (15): Graceful termination request
- **SIGKILL** (9): Forced termination (can't catch)
- **SIGSTOP** (19): Suspend process
- **SIGCONT** (18): Resume process
- **SIGHUP** (1): Hangup, reload config
- **SIGINT** (2): Interrupt (Ctrl+C)

### 4. Process Groups
- Multiple processes can belong to one group
- Signals sent to group affect all processes
- Session: Collection of process groups

## Hands-on Lab: Process Management

### Lab Steps

```bash
# 1. View process tree
ps aux
pstree

# 2. Run process in background
sleep 3600 &

# 3. List background jobs
jobs -l

# 4. Get process details
ps -fp 2345

# 5. Monitor process in real-time
top -p 2345

# 6. Send signal to process
kill -TERM 2345

# 7. Create long-running process
while true; do echo "Running"; sleep 1; done &
BG_PID=$!

# 8. Suspend process
kill -STOP $BG_PID

# 9. Resume process
kill -CONT $BG_PID

# 10. Kill process by name
killall sleep
```

## Validation

Verify your process management skills:

```bash
# Can you view process trees?
pstree && echo "✓ Process tree works"

# Can you run background jobs?
sleep 100 & jobs -l && kill %1

# Can you send signals?
kill -0 $$ && echo "✓ Can send signals"

# Can you list processes?
ps aux | grep bash && echo "✓ Process listing works"
```

## Cleanup

Kill any remaining background processes:

```bash
# Kill background jobs
kill %1 %2 %3 2>/dev/null

# Kill any stray sleep processes
killall sleep 2>/dev/null
```

## Common Mistakes

1. **Using SIGKILL for graceful shutdown**: Use SIGTERM first, then SIGKILL
2. **Forgetting exit codes**: Check `$?` after commands
3. **Zombie processes**: Parent must wait() for children
4. **Mixing fg/bg**: Use `fg %1`, not `fg 1`
5. **Resource limits**: ulimit can prevent fork bombs

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Zombie processes | Kill parent process |
| Can't kill process | Use SIGKILL (kill -9) as last resort |
| Process won't stop | Check if ignoring SIGTERM |
| Too many processes | Check for fork bombs, use limits |
| Find process by name | Use: pgrep, pidof, ps grep |

## Next Steps

1. Move to **03-threads-and-concurrency** for concurrent execution
2. Learn about process scheduling
3. Study inter-process communication (IPC)
4. Explore systemd and service management
5. Learn container process isolation (cgroups)

## Additional Resources

- Process signals: `man 7 signal`
- Process management: `man 2 fork`, `man 2 exec`
- Process monitoring: `man ps`, `man top`

