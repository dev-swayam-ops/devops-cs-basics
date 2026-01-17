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

Every process goes through a series of states from creation to termination. Understanding these states is crucial for effective process management.

**Creation**
- A parent process calls `fork()` to create a child process
- `fork()` is the only way to create a new process in Linux
- The child is a complete copy of the parent at that moment - same code, data, file descriptors
- Both parent and child continue executing from the point after `fork()`
- The child has a new PID but the same PPID (parent's PID)

**Running**
- The process is actively executing instructions on the CPU
- Only one process per CPU core can run at a time
- The scheduler decides which process gets CPU time (time slice or quantum)
- Multiple processes appear to run simultaneously because the kernel switches between them rapidly
- A process can run for just a few milliseconds before being preempted (paused)

**Waiting (Blocked)**
- The process has requested a resource that isn't immediately available
- Common reasons: waiting for I/O (disk read, network packet), waiting for user input, waiting for a lock
- While waiting, the process doesn't consume CPU time - the kernel moves it to the wait queue
- Once the resource is available (file data arrives, input is received), the kernel moves it back to runnable state
- This is essential - if waiting processes consumed CPU, the system would be frozen

**Stopped**
- The process is paused/suspended by a signal (typically `SIGSTOP`)
- Unlike waiting, a stopped process is explicitly suspended by user or kernel
- Can be resumed later with `SIGCONT` signal
- Useful for job control in shells (pause a background process, then resume it)

**Zombie**
- The process has terminated and exited, but its parent hasn't yet called `wait()` to read its exit status
- The child's resources are freed, but a small entry remains in the process table
- This entry holds the exit code until the parent reads it
- If a parent dies without reading child's exit status, zombie becomes orphaned and `init` adopts it
- Too many zombies indicate a buggy parent process that doesn't call `wait()`

**Terminated**
- The process has completely exited and its entry is removed from the process table
- Can happen when: process calls `exit()`, last thread terminates, fatal signal received
- Child's exit code is delivered to parent via `wait()`
- All resources (memory, file descriptors, etc.) are released back to the system

### 2. Process Creation

The creation of new processes follows a strict pattern in Unix/Linux that separates process creation from program execution.

**fork() System Call**
- Creates a new child process that is an exact duplicate of the parent at that moment
- Returns 0 to the child and the child's PID to the parent (or -1 on error)
- Very efficient - uses copy-on-write, so child doesn't immediately duplicate all memory
- Both parent and child have separate memory spaces - modifications in one don't affect the other
- File descriptors are shared (unless explicitly closed) - both see the same open files
- After `fork()`, parent and child run concurrently, completely independently
- If parent doesn't call `wait()`, child may become a zombie

**exec() System Call**
- Replaces the current process's memory with a new program
- Takes the new program's path and arguments as parameters
- Does NOT create a new process - it transforms the current one
- The PID remains the same; the process keeps its identity
- Useful because the child process doesn't want to run the parent's code
- Once `exec()` is called, there's no going back - the old code is gone
- If `exec()` fails, the process terminates (since old code is replaced)

**fork() + exec() Pattern**
- The standard two-step process to launch a new program in Linux
- Parent calls `fork()` to create a child
- Child calls `exec()` to run a different program (like `/bin/bash`, `/bin/ls`)
- Parent continues with its own code while child runs the new program
- This separation allows parent to control the child: redirect I/O before `exec()`, wait for completion
- Every shell command you type follows this pattern: shell forks, child execs the command

**Independent Execution**
- After `fork()` + `exec()`, parent and child are truly independent
- Parent can continue running while child executes
- Parent can wait for child with `wait()` to synchronize
- Child cannot affect parent's memory or file descriptors
- Both have their own variables, stacks, and execution state

### 3. Process Signals

Signals are software interrupts that provide a way to handle asynchronous events and control processes.

**What Are Signals**
- Notifications sent to a process by the kernel or other processes
- Interrupt the normal execution flow to handle the signal
- Each signal has a name and number (0-64, standardized across systems)
- Most signals can be caught/blocked, but some are uncatchable for security reasons
- When a process receives a signal, the default action occurs unless the process installed a handler

**Common Signals**

**SIGTERM (15) - Termination Signal**
- Graceful termination request - "please exit"
- The default action is process termination, but the process can catch it and clean up
- Allows process to release resources, close connections, save state
- The respectful way to stop a process - gives it a chance to shut down properly
- Used by most init systems and service managers for graceful shutdown

**SIGKILL (9) - Force Termination**
- Immediately kills the process - cannot be caught, blocked, or ignored
- No cleanup possible - resources may leak, connections may be aborted
- Only use when SIGTERM doesn't work (hung/stuck process)
- The nuclear option - guaranteed to stop a process but can leave mess
- System administrators use this as last resort

**SIGSTOP (19) - Suspend Process**
- Pauses the process - cannot be caught or ignored
- Process stops consuming CPU but remains in memory with its state
- Useful for pausing a runaway process without killing it
- Can be resumed later with SIGCONT
- Jobs control in shells uses this for Ctrl+Z

**SIGCONT (18) - Continue Process**
- Resumes a stopped process from where it paused
- Only works if process is currently stopped by SIGSTOP
- Allows process to continue execution in foreground or background
- Essential for job control - pause long operations and resume later

**SIGCHLD - Child Process Event**
- Sent to parent when a child process terminates
- Notifies parent that child status is available to read
- Allows parent to detect child termination and call `wait()`
- Important for proper cleanup - prevents zombie accumulation
- Parent that ignores SIGCHLD has child automatically reaped

### 4. Process Control

Process control is how users and the shell manage running processes - starting, pausing, stopping them.

**Foreground Processes**
- The default when you run a command in the shell
- Process runs and the shell waits for it to complete
- You cannot type commands while process is running
- Process receives keyboard input (Ctrl+C, Ctrl+Z)
- When process exits, shell prompt returns and you can type next command
- Useful for interactive programs or one-off tasks

**Background Processes**
- Started by appending `&` to the command: `sleep 3600 &`
- Process runs independently while shell prompt returns immediately
- You can type new commands while background process executes
- Background process cannot read keyboard input (unless explicitly redirected)
- Useful for long-running tasks that don't need interaction: backups, servers, data processing
- Shell maintains list of background jobs - you can manage them

**Job Control**
- Shells (bash, zsh) track multiple processes in a construct called "job"
- Each background process is a job with a job number `[1]`, `[2]`, etc.
- `jobs` command lists all background jobs in current shell session
- Each job has a status: Running, Stopped, Terminated
- Job numbers allow referring to processes: `fg %1` brings job 1 to foreground
- Jobs are local to a shell session - closing shell terminates its jobs

**Ctrl+Z - Suspend Foreground Process**
- Sends SIGSTOP signal to currently running foreground process
- Process pauses and shell regains control
- Shell displays job number and status: `[1]+ Stopped sleep 3600`
- You can then use `bg` to resume in background or `fg` to resume in foreground
- Useful for pausing long-running processes without killing them, then deciding what to do

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

