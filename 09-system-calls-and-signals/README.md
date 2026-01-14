# 09 - System Calls and Signals

## What You'll Learn

Master system calls and signal handling:
- Common system calls and their purposes
- Syscall lifecycle and context switching
- Signal types and handling
- Signal handlers and masks
- Inter-process communication
- Error handling and return values
- Performance implications

## Prerequisites

- Completed **08-linux-kernel-basics**
- Understanding of processes and kernel
- Basic C knowledge helpful

## Key Concepts

### 1. Common System Calls
- **File I/O**: open(), close(), read(), write()
- **Process**: fork(), exec(), exit(), wait()
- **Memory**: mmap(), brk()
- **IPC**: pipe(), socket(), msgget()
- **Signals**: kill(), signal(), sigaction()
- **Time**: sleep(), nanosleep(), time()

### 2. System Call Process
```
User Space
  |
  | context switch
  v
Kernel Space (privileged mode)
  - Perform operation
  - Access hardware
  |
  | context switch
  v
User Space
  - Return value or error
```

### 3. Signal Types
- **SIGTERM (15)**: Graceful termination
- **SIGKILL (9)**: Force termination (uncatchable)
- **SIGSTOP (19)**: Suspend (uncatchable)
- **SIGCONT (18)**: Resume
- **SIGCHLD**: Child process terminated
- **SIGUSR1, SIGUSR2**: User-defined

### 4. Error Handling
- Check return value of every syscall!
- Negative return = error
- errno variable contains error code
- strerror() or perror() to get message

## Hands-on Lab: System Calls and Signals

### Lab Steps

```bash
# 1. Trace system calls
strace ls /tmp
strace -e open,read cat /etc/hostname

# 2. Count syscalls
strace -c ls /tmp
strace -c curl http://example.com | head

# 3. Filter by syscall type
strace -e file ls /tmp      # file operations
strace -e process bash      # process operations
strace -e network nc localhost 22  # network

# 4. Show syscall arguments
strace -v -e write bash -c "echo hello"

# 5. Catch signals
bash -c 'trap "echo caught SIGTERM" TERM; sleep 100' &
sleep 1
kill -TERM $!

# 6. Send custom signals
bash -c 'trap "echo USR1" USR1; sleep 100' &
BASH_PID=$!
kill -USR1 $BASH_PID
kill -TERM $BASH_PID

# 7. Block signals (bash built-in)
bash -c 'set -b; trap "" TERM; sleep 10 &' &
sleep 1
kill -TERM $!

# 8. Examine /proc for signals
cat /proc/$$/status | grep -i sig

# 9. Use timeout for syscall limits
timeout 5 sleep 100
echo "Exit code: $?"

# 10. System call timing
time strace -c curl http://example.com
```

## Validation

Verify your understanding:

```bash
# Can you trace syscalls?
strace echo "test" && echo "✓ Syscall tracing works"

# Can you count syscalls?
strace -c echo "test" && echo "✓ Syscall counting works"

# Can you send signals?
bash -c 'sleep 100' & kill -0 $! && echo "✓ Signal sending works"

# Can you understand context switching?
echo "✓ Context switch concepts understood"
```

## Cleanup

```bash
killall sleep bash strace 2>/dev/null
```

## Common Mistakes

1. **Ignoring syscall errors**: Always check return values!
2. **Uncatchable signals**: Can't catch SIGKILL or SIGSTOP
3. **Signal handler side effects**: Keep handlers simple
4. **Blocking in signal handler**: Can cause deadlock
5. **System call interruption**: Need SA_RESTART flag

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Syscall fails | Check: return value, errno, strace output |
| Signal not delivered | Check: process alive, signal not blocked |
| Slow syscalls | Profile: strace -c, reduce syscall count |
| Zombie processes | Ensure: parent calls wait() for children |
| Race condition | Synchronize: signals, IPC, atomicity |

## Next Steps

1. Move to **10-scheduling-and-performance** for optimization
2. Learn about advanced IPC (pipes, sockets, semaphores)
3. Study signal-safe functions
4. Explore real-time signals
5. Learn about seccomp for syscall filtering

## Additional Resources

- System calls: `man 2 syscalls`
- Signals: `man 7 signal`, `man 2 sigaction`
- strace: `man strace`
- System call reference: https://man7.org/linux/man-pages/

