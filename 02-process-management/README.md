# 02 - Process Management

## What You'll Learn

- Process lifecycle and states
- Process creation with fork() and exec()
- Parent-child process relationships
- Process signals and termination
- Background and foreground processes
- Basic process monitoring and control

## Prerequisites

- Completed **01-os-fundamentals**
- Understanding of PID and PPID
- Basic Linux commands

## Key Concepts

### 1. Process Lifecycle
- **Creation**: Parent calls fork() → child process created
- **Running**: Process executes instructions on CPU
- **Waiting**: Process blocked waiting for resource (I/O, input, etc.)
- **Stopped**: Suspended by signal (SIGSTOP)
- **Zombie**: Terminated but parent hasn't cleaned up with wait()
- **Terminated**: Process completely exited

### 2. Process Creation
- **fork()**: Creates exact copy of parent process
- **exec()**: Replaces process memory with new program
- **fork() + exec()**: Standard way to launch new program
- Parent and child continue independently after fork()

### 3. Process Signals
- **SIGTERM (15)**: Graceful termination request
- **SIGKILL (9)**: Force termination (cannot be caught)
- **SIGSTOP (19)**: Suspend process (cannot be caught)
- **SIGCONT (18)**: Resume suspended process
- **SIGCHLD**: Child process terminated

### 4. Process Control
- **Foreground**: Process runs until completion
- **Background**: Process runs in background (appended with &)
- **Jobs**: List of background processes in current shell
- **Ctrl+Z**: Suspend current foreground process

## Hands-on Lab: Process Management

### Lab Overview
Control processes and understand process relationships.

### Lab Commands

```bash
# 1. Start a background process
sleep 3600 &

# Expected output:
# [1] 2345

# 2. List background jobs
jobs -l

# Expected output:
# [1]+ 2345 Running   sleep 3600 &

# 3. View process details
ps -fp 2345

# Expected output:
# UID  PID PPID C STIME TTY STAT TIME CMD
# user 2345 1234 0 10:30 pts/0 S    0:00 sleep 3600

# 4. Check process tree
pstree -p $$

# Expected output shows parent-child hierarchy

# 5. Send SIGTERM to process
kill -TERM 2345

# Expected output (in shell):
# [1]- Terminated sleep 3600

# 6. Start another background process
sleep 7200 &

# 7. Suspend current shell
Ctrl+Z  # (Don't actually run - demonstration)

# 8. Resume in background
bg

# 9. Bring to foreground
fg %1

# 10. Kill by name (after starting another)
sleep 500 &
killall sleep
```

## Validation

Verify your understanding:

```bash
# Can you start background jobs?
sleep 100 & jobs -l

# Can you send signals?
kill -0 $$ && echo "✓ Signal sending works"

# Can you list processes?
ps aux | grep bash && echo "✓ Process listing works"

# Can you view process tree?
pstree -p $$ && echo "✓ Tree viewing works"
```

## Cleanup

```bash
# Kill all sleep processes
killall sleep 2>/dev/null

# Kill any background jobs
kill %1 %2 %3 2>/dev/null
```

## Common Mistakes

1. **Using SIGKILL immediately**: Use SIGTERM first, SIGKILL as last resort
2. **Forgetting &**: Process runs in foreground and blocks shell
3. **Zombie processes**: Parent must wait() for terminated children
4. **Mixing fg/bg**: Use `fg %1`, not `fg 1`
5. **Lost background jobs**: Shell exit kills background processes

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't kill process | Try: `kill -9 PID` (SIGKILL) |
| Zombie process | Kill parent process: `kill -9 PPID` |
| Process won't start | Check: file exists, executable, permissions |
| Background job suspended | Resume: `bg` or `kill -CONT %1` |
| Can't find process | Use: `pgrep processname` or `pidof processname` |

## Next Steps

1. Complete the 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Practice with `cheatsheet.md` commands
4. Move to **03-threads-and-concurrency** after completion

## Additional Resources

- Process signals: `man 7 signal`
- Kill command: `man kill`
- Process management: `man ps`, `man jobs`, `man fg`

