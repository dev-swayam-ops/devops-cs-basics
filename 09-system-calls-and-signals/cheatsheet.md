# 09 - System Calls and Signals Cheatsheet

## Signal Numbers and Names

| Signal | Number | Meaning | Default |
|--------|--------|---------|---------|
| SIGHUP | 1 | Hangup (terminal closed) | Terminate |
| SIGINT | 2 | Interrupt (Ctrl+C) | Terminate |
| SIGQUIT | 3 | Quit (Ctrl+\) | Core dump |
| SIGKILL | 9 | Kill (forced) | Terminate (can't catch) |
| SIGTERM | 15 | Terminate (graceful) | Terminate |
| SIGSTOP | 19 | Stop (pause) | Stop (can't catch) |
| SIGCONT | 18 | Continue (resume) | Continue |
| SIGCHLD | 17 | Child died | Ignore |
| SIGUSR1 | 10 | User-defined | Terminate |
| SIGUSR2 | 12 | User-defined | Terminate |

## Kill Command

| Command | Purpose | Example |
|---------|---------|---------|
| `kill -TERM [PID]` | Graceful termination | Signal 15 |
| `kill -9 [PID]` | Force kill | SIGKILL, can't catch |
| `kill -STOP [PID]` | Pause process | Signal 19 |
| `kill -CONT [PID]` | Resume process | Signal 18 |
| `kill -l` | List all signals | Signal reference |
| `kill -SIGTERM [PID]` | By name | Same as -TERM |
| `kill -[NUMBER] [PID]` | By number | 15 = SIGTERM |

## Process Control Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `pkill -TERM name` | Kill by name | Multiple processes |
| `pkill -9 name` | Force kill by name | Last resort |
| `pkill -l name` | List matching processes | Show PIDs first |
| `killall TERM name` | Old kill all | Process name |
| `killall -9 name` | Force kill all | Forced version |

## Process Signals Behavior

| Action | SIGTERM | SIGKILL | SIGSTOP |
|--------|---------|---------|---------|
| Catchable | Yes | No | No |
| Ignorable | Yes | No | No |
| Graceful exit | Yes | No | N/A |
| Cleanup | Yes | No | N/A |
| Can resume | After cont | After cont | Yes |

## System Call Examples

| Syscall | Purpose | From |
|---------|---------|------|
| fork | Create process copy | C, bash |
| exec | Replace program | C, bash |
| exit | Terminate | C, bash |
| wait | Wait for child | C |
| kill | Send signal | C, bash |
| read | Read data | C |
| write | Write data | C |

## Trap Syntax (Bash)

| Command | Purpose | Example |
|---------|---------|---------|
| `trap "cmd" SIGNAL` | Handle signal | trap "echo done" TERM |
| `trap "" SIGNAL` | Ignore signal | trap "" TERM |
| `trap - SIGNAL` | Reset default | trap - TERM |
| `trap "cmd" EXIT` | On script exit | trap "cleanup" EXIT |
| `trap -p` | Print all traps | Show current |

## Process States

| State | Letter | Meaning |
|-------|--------|---------|
| Running | R | Executing on CPU |
| Sleeping | S | Waiting for I/O |
| Disk sleep | D | Uninterruptible wait |
| Stopped | T | Paused (SIGSTOP) |
| Zombie | Z | Terminated, awaiting reap |
| Trace stop | t | Being debugged |

## Checking Processes

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | Show all with state | Find zombies (Z) |
| `ps aux \| grep [PID]` | Find specific | Check status |
| `jobs -l` | Shell job list | With PIDs |
| `fg %1` | Foreground job | Bring to front |
| `bg %1` | Background job | Resume in background |

## Common Signal Patterns

| Action | Command | Explanation |
|--------|---------|--------------|
| Graceful shutdown | kill -TERM | App can clean up |
| Force shutdown | kill -9 | Immediate, no cleanup |
| Pause process | kill -STOP | Like Ctrl+Z |
| Resume process | kill -CONT | Continue running |
| Reload config | kill -HUP | Some apps re-read config |

## Zombie Prevention

| Method | How | Example |
|--------|-----|---------|
| wait() | C call | wait(&status) |
| Process reaping | Systemd | Automatic |
| Double fork | Daemonize | Fork, then fork again |

## Process Group Signals

```bash
# Send to process
kill -TERM 12345

# Send to all in group
kill -TERM -12345      # Negative = process group

# Send to all matching
pkill -TERM -f sleep   # All matching pattern
```

## Safe Signal Handlers

Functions safe in signal handlers:
- write()
- signal()
- _exit()
- Some others (limited)

Avoid:
- malloc/free
- printf
- Most library calls

## Quick Reference

```bash
# List signals
kill -l

# Kill gracefully
kill -TERM $PID

# Force kill
kill -9 $PID

# Multiple processes
pkill -TERM sleep

# Trap in script
trap "echo Caught" TERM

# Ignore signal
trap "" TERM

# Check state
ps aux | grep PID
```
