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

These are the fundamental syscalls for process and file operations. Understanding them is crucial for systems programming.

**Process Control System Calls**

**fork()**
- Creates new child process as exact copy of parent
- Returns 0 to child, child's PID to parent, -1 on error
- Both parent and child continue executing from fork() point
- Child has separate memory space - changes in child don't affect parent
- File descriptors are duplicated - child shares open files with parent
- Most common way to create new processes in Unix
- Used by shell when you type a command

**exec() Family (execve, execvp, execl, etc.)**
- Replaces current process with new program
- Does NOT create new process - transforms existing one
- PID remains same, but code, memory, registers all change
- Return value: only returns on error (-1)
- Often used with fork(): parent forks child, child execs new program
- Cannot return to old code after exec - it's gone
- Kernel preserves file descriptors and signal handlers (by default)

**exit(int status)**
- Process terminates
- Returns exit code to parent (0-255, 0 = success)
- All resources freed: memory, file descriptors, etc.
- Parent can retrieve exit code with wait()
- If parent exits first without waiting, init adopts child and reaps it
- exit() flushes and closes streams
- Called by `return` in main() function

**wait() and waitpid()**
- Parent blocks waiting for child to terminate
- Returns child's PID and exit status
- Allows parent to learn why child exited (normal, signal, core dump)
- If multiple children running, parent can wait for specific one (waitpid) or any (wait)
- Essential for zombie prevention
- Parent continues executing after child terminates

**File Operations System Calls**

**open(const char *path, int flags)**
- Opens file or creates it
- Returns file descriptor (small integer identifying open file)
- Flags specify: read/write, create if missing, truncate, append, etc.
- Same process can have multiple file descriptors to same file
- File descriptors 0, 1, 2 are stdin, stdout, stderr (standard)
- Limited per process: typically 1024 file descriptors

**read(int fd, void *buf, size_t count)**
- Reads up to `count` bytes from file descriptor into buffer
- Returns number of bytes actually read (may be less than requested)
- If at end of file, returns 0
- On error, returns -1
- Blocks until data available (unless non-blocking mode)
- Updates file position - next read continues from where this left off

**write(int fd, const void *buf, size_t count)**
- Writes `count` bytes from buffer to file descriptor
- Returns number of bytes written (may be less than requested)
- On error, returns -1
- Blocks if output buffer full (unless non-blocking)
- File position updated - next write continues from end
- Important: might not write all bytes in single call

**close(int fd)**
- Closes file descriptor
- Flushes any pending writes
- File descriptor becomes available for reuse
- All open files automatically closed when process exits
- Important to close to avoid resource leaks
- Error on closing already-closed descriptor

### 2. Signal Types and Handling

Signals are software interrupts that notify processes of events. Different signals have different meanings and can be handled differently.

**Termination Signals**

**SIGTERM (15)**
- Termination signal - "please exit"
- Default action: terminate process
- Can be caught/handled by application
- Allows graceful shutdown: close connections, save state
- Standard way to stop processes
- `kill` command sends SIGTERM by default

**SIGKILL (9)**
- Force kill - cannot be caught, blocked, or ignored
- Immediate termination, no cleanup possible
- Last resort when SIGTERM doesn't work
- Process receives no chance to react
- Resources may leak, connections abruptly closed
- Dangerous if process was in middle of update

**SIGINT (2)**
- Interrupt signal - sent by Ctrl+C
- Default action: terminate
- Can be caught to do cleanup before exit
- Default works well for interactive programs
- Shell uses it to stop foreground process

**SIGQUIT (3)**
- Quit signal - sent by Ctrl+\\
- Like SIGINT but also dumps core (memory snapshot)
- Useful for debugging
- Default action: terminate with core dump

**Process Control Signals**

**SIGSTOP (19)**
- Pause process - cannot be caught
- Not termination, just suspension
- Process doesn't consume CPU but stays in memory
- Sent by Ctrl+Z in interactive shell
- Only SIGCONT can resume stopped process

**SIGCONT (18)**
- Continue - resumes stopped process
- Only meaningful for stopped process
- Allows: pause long operation, then resume
- Used with job control in shells

**Terminal-Related Signals**

**SIGHUP (1)**
- Hangup - sent when terminal closes
- Default action: terminate
- Used by daemons to reload configuration
- When you SSH and connection drops, SIGHUP sent to all processes
- Important for servers: trap it to reload config without restart

**User-Defined Signals**

**SIGUSR1 (10) and SIGUSR2 (12)**
- No predefined meaning - application decides
- Useful for inter-process communication
- Example: daemon might reload config on SIGUSR1
- Real-time signals (SIGRTMIN to SIGRTMAX) can queue multiple signals

**Child Process Signals**

**SIGCHLD (17)**
- Sent to parent when child terminates
- Default action: ignore
- Parent can trap to know when child exits
- Allows cleaning up zombie (calling wait())
- If parent ignores, child automatically reaped

### 3. Signal Delivery and Handling

When a signal is sent to a process, the kernel must deliver it and the process can respond by running a signal handler.

**Signal Delivery Flow**
1. Some event occurs (timer expired, user presses Ctrl+C, etc.)
2. Kernel sends signal to process
3. Signal marked as pending for process
4. At next opportunity, kernel delivers signal to process
5. Process either: runs handler (if one installed) or default action
6. Process resumes normal execution

**Pending Signals**
- Signal generated but not yet delivered
- Process blocked on I/O: signal queued, delivered when unblocked
- Multiple signals of same type: only one marked pending (not queued)
- Real-time signals (SIGRTMIN+) do queue
- Unhandled signal causes default action

**Default Actions**
- Terminate: process exits
- Ignore: signal discarded, process continues
- Stop: process pauses
- Continue: process resumes
- Core dump: process exits and writes memory dump

**Signal Handlers**
- Application-provided function called when signal arrives
- Registered with `signal()` or `sigaction()` syscall
- Handler runs in signal context (special context, not in process)
- Process pauses where it was, handler executes, process resumes
- After handler returns, process continues
- Can modify process behavior (exit, continue, ignore)

**Signal Trapping (Bash)**
- `trap command SIGNAL` - sets up handler in bash script
- Example: `trap 'rm /tmp/tempfile' EXIT` - cleanup before exit
- Example: `trap 'echo Interrupted!' SIGINT` - handle Ctrl+C
- Command executed when signal arrives
- Common for: cleanup, graceful shutdown, reloading config

**Blocking Signals**
- `sigprocmask()` blocks specific signals
- Blocked signals still sent but not delivered
- Useful: protect critical section from being interrupted
- Multiple signals can be blocked
- Blocking SIGKILL/SIGSTOP has no effect (always delivered)

### 4. Zombie Processes

A zombie is a process that has exited but hasn't been cleaned up by its parent. Understanding zombies is important for process management.

**How Zombies Form**
1. Child process finishes execution or dies
2. Child calls exit() or receives fatal signal
3. Child's memory and resources freed by kernel
4. Small entry remains in process table containing:
   - PID
   - Exit code or signal that killed it
   - Parent PID
5. Parent can retrieve this info with wait()
6. After parent reads it, entry removed

**Zombie vs Terminated**
- Terminated: process completely removed, PID can be reused
- Zombie: process exited but entry lingers in process table
- Zombie takes minimal resources (small kernel data structure)
- But PID still held - if too many zombies, can't create new processes

**Why Zombies Exist**
- Design: children should notify parent of exit
- Parent may be doing other things, checks child status later
- Prevents info loss: exit code held until parent reads it
- If parent dies without reading, init (PID 1) adopts and reaps zombie

**Detecting Zombies**
- `ps aux` shows process state - zombie has `<defunct>` in command
- `ps` shows `Z` state for zombie
- `cat /proc/[zombie_pid]/status` shows `State: Z`

**Preventing Zombies**
- Parent must call wait() or waitpid()
- Trap SIGCHLD signal and call wait() in handler
- Or ignore SIGCHLD: `signal(SIGCHLD, SIG_IGN)` - automatic cleanup
- Server programs: loop calling waitpid() non-blocking after each accept()

**Handling Accumulation**
- Parent dies: init adopts all children and reaps them
- Zombie harmless but indicates parent bug
- Parent not waiting for children - will eventually fail
- Long-running parent with child zombies: parent probably broken

### 5. Signal Safety and Async-Signal-Safe Functions

Not all functions are safe to call from signal handlers. Understanding which are safe is critical for robust signal handling.

**Why Signal Handler Safety Matters**
- Signal can interrupt process at ANY point
- Process might be in middle of function call
- If signal handler calls same function, can corrupt internal state
- Example: signal interrupts malloc(), handler calls malloc() → deadlock
- Locks don't work - only one thread, same process

**Async-Signal-Safe Functions**
- Functions that can safely be called from signal handler
- Typically just system calls
- Cannot call library functions (malloc, printf, etc.)
- Safe functions listed in `man 7 signal-safety`
- Examples: write, read, signal, exit, _Exit, kill, pause

**Common Safe Functions**
- I/O: `write()` (stdout, files, sockets)
- Process: `exit()`, `_exit()`, `getpid()`
- Signals: `signal()`, `sigaction()`
- Simple: `memcpy()`, string ops

**Unsafe Functions (Never Call in Handler)**
- `printf()` - uses locks, buffering
- `malloc()`, `free()` - uses locks
- `sleep()` - uses signals internally
- Most library functions - not designed for signal context
- Anything involving locks

**Best Practice**
- Signal handler should do minimal work
- Set a volatile global flag
- Return from handler
- Main program checks flag and takes action
- Example: catch SIGTERM, set flag, main loop checks flag and exits gracefully

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

