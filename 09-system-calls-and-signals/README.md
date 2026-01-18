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

**fork()**: Creates child process (copy of parent). Returns 0 to child, child's PID to parent. Used by shell to run commands. Child has separate memory but shares open files.

**exec()**: Replaces current process with new program. PID stays same, code/memory change. Used with fork(): `fork() → exec()` pattern. No return on success (only on error).

**exit(status)**: Process terminates, returns status (0-255) to parent. Resources freed. Parent retrieves status with `wait()`.

**wait() / waitpid()**: Parent blocks until child exits. Retrieves exit code. Essential to prevent zombies. `waitpid()` for specific child.

**File I/O**: `open()`, `read()`, `write()`, `close()`. All take/return file descriptors (0=stdin, 1=stdout, 2=stderr).

**DevOps Relevance**: Understand fork/exec pattern. Optimize systems by reducing syscall overhead in hot paths. Monitor open files with `lsof`.

### 2. Signal Types

**Termination**:
- **SIGTERM (15)**: Graceful shutdown (default `kill`). Can be caught. Application can cleanup.
- **SIGKILL (9)**: Force kill. Uncatchable. Use only as last resort (dirty exit).
- **SIGINT (2)**: Ctrl+C. Usually caught for cleanup.

**Process Control**:
- **SIGSTOP (19)**: Pause (uncatchable). Resume with SIGCONT.
- **SIGCONT (18)**: Resume stopped process.

**Daemon Signals**:
- **SIGHUP (1)**: Hangup. Daemons trap this to reload config without restart.
- **SIGCHLD (17)**: Child exited. Parent can trap to cleanup zombies.

**User-Defined**:
- **SIGUSR1, SIGUSR2**: Application-specific (e.g., reload config).

**DevOps Relevance**: `kill -15 PID` asks nicely. `kill -9 PID` forces. Daemons should handle SIGHUP for zero-downtime restarts.

### 3. Signal Handling

**Delivery Flow**: Signal generated → marked pending → delivered when process unblocks → handler runs (if installed) or default action taken.

**Signal Trapping in Bash**:
```bash
trap 'cleanup' SIGTERM        # Run cleanup() on SIGTERM
trap 'echo reloading' SIGHUP  # Reload config on SIGHUP
trap - SIGTERM               # Remove trap, restore default
```

**Default Actions**: Terminate, ignore, stop, continue, core dump.

**Best Practice**: Handler should set volatile flag, return. Main loop checks flag and takes action. Don't do heavy work in handler.

**Blocking Signals**: Can block (delay delivery) but SIGKILL/SIGSTOP always get through.

**DevOps Action**: Scripts should trap EXIT/TERM for cleanup. Daemons should trap SIGHUP to reload config without restart.

### 4. Zombie Processes

**What**: Process exited but parent hasn't called `wait()`. Kernel keeps small entry (exit code) in process table. PID held until parent reads it.

**Detection**: `ps aux | grep Z` or `ps aux | grep defunct`. Shows `<defunct>` in output.

**Prevention**: Parent must call `wait()` or `waitpid()`. Or ignore SIGCHLD: `trap '' SIGCHLD`.

**Accumulation Risk**: Parent dies → init adopts children and reaps them. But long-running parent with zombies = parent bug (not calling wait()).

**DevOps Action**: Zombie processes harmless but indicate application bug. Parent should be calling `wait()` for its children. Check with `ps aux | grep Z`.

### 5. Signal Safety

**Problem**: Signal can interrupt process mid-function. If handler calls same function = corruption (e.g., malloc() interrupted by malloc()).

**Safe Functions**: Limited set of syscalls/functions safe to call in signal handler. Read `man 7 signal-safety`.

**Safe**: `write()`, `read()`, `exit()`, `kill()`, `signal()`, simple ops like `memcpy()`.

**Unsafe**: `printf()`, `malloc()`, `free()`, `sleep()`, most library functions (they use locks).

**Best Practice**: Handler does minimal work—just set volatile global flag. Return. Main program checks flag and takes action.

**Example**:
```bash
trap 'SIGTERM_RECEIVED=1' SIGTERM
while true; do
  [ "$SIGTERM_RECEIVED" = 1 ] && break  # Check flag
  # do work
done
```

**DevOps Relevance**: Write safe shutdown scripts. Don't do complex work in signal handlers.

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

