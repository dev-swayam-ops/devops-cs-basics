# 01 - OS Fundamentals

## What You'll Learn

In this module, you'll understand:
- Operating system structure and purpose
- Kernel vs user mode
- Process and thread basics
- System calls and kernel interfaces
- System resources (CPU, memory, I/O)
- Basic OS administration

## Prerequisites

- Linux command line basics
- Text editor knowledge (nano, vim)
- No programming required

## Key Concepts

### 1. Kernel vs User Mode
- **Kernel Mode**: Full hardware access, system operations
- **User Mode**: Restricted access, application code runs here
- **System Calls**: Bridge between user and kernel

### 2. Process Basics
- Process: Running instance of a program
- Process ID (PID): Unique identifier
- Parent Process ID (PPID): Parent's PID
- Process State: Running, waiting, stopped, zombie

### 3. System Resources
- **CPU**: Execution units
- **Memory**: RAM for storage
- **I/O**: Disk, network, terminals
- **Scheduling**: Fair resource allocation

### 4. File System
- Hierarchical directory structure
- Inodes and file metadata
- Permissions and ownership
- Mount points

## Hands-on Lab: OS Exploration

### Lab Steps

```bash
# 1. Get OS information
uname -a

# 2. Check uptime
uptime

# 3. View CPU information
lscpu

# 4. Check memory usage
free -h

# 5. Check disk usage
df -h

# 6. View current user
whoami

# 7. List processes
ps aux

# 8. Check running services
systemctl status

# 9. View system logs
journalctl -n 10

# 10. Check file system hierarchy
ls -la /
```

## Validation

Verify you understand the concepts:

```bash
# Can you get OS info?
uname -a && echo "✓ OS info accessible"

# Can you check resources?
free -h && echo "✓ Memory check works"

# Can you list processes?
ps aux | head && echo "✓ Processes visible"

# Can you check uptime?
uptime && echo "✓ Uptime accessible"
```

## Cleanup

No changes to clean up. This was read-only exploration.

## Common Mistakes

1. **Confusing PID and PPID**: PID is process's ID, PPID is parent's ID
2. **Memory units**: 1GB = 1024MB, not 1000MB
3. **Process states**: R=running, S=sleeping, Z=zombie, D=disk wait
4. **Permissions**: Read=4, Write=2, Execute=1
5. **Root access**: Don't run everything as root!

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't run command | Check: permissions, PATH, command exists |
| Permission denied | Check: file permissions, user groups |
| Out of memory | Check: free -h, kill unused processes |
| Disk full | Check: df -h, remove old logs/files |
| System slow | Check: top, iostat, network connections |

## Next Steps

1. Move to **02-process-management** to understand processes deeper
2. Learn process signals and lifecycle
3. Explore process monitoring tools
4. Study resource limits with ulimit
5. Understand systemd and init systems

## Additional Resources

- Linux kernel documentation: https://www.kernel.org/doc/
- Man pages: `man 7 man-pages`
- Linux foundation: https://www.linuxfoundation.org/

