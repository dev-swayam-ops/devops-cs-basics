# 15 - Observability and Debugging Cheatsheet

## Logging Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `journalctl -n 50` | Last 50 messages | View recent logs |
| `journalctl -f` | Follow live | Real-time monitor |
| `journalctl -p err` | Priority level | Only errors |
| `journalctl -u service` | By unit | Specific service |
| `journalctl --since "1 hour ago"` | Time range | Last hour |
| `journalctl -o json` | JSON format | Machine readable |
| `tail -f /var/log/file` | Follow file | Real-time tail |
| `grep pattern /var/log/file` | Search | Find matching |

## Tracing Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `strace -e trace=open cmd` | Filter syscalls | Only open() |
| `strace -c cmd` | Count syscalls | Statistics |
| `strace -e trace=file cmd` | File operations | File syscalls |
| `strace -p PID` | Attach to PID | Debug running |
| `ltrace -e printf cmd` | Library calls | Specific function |
| `ltrace -c cmd` | Count calls | Function stats |

## Monitoring Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `top` | Interactive monitor | Real-time view |
| `top -p PID` | Single process | One PID only |
| `ps -eo pid,%cpu,%mem --sort=-%cpu` | Sort by CPU | CPU hogs |
| `ps -eo pid,%mem,rss --sort=-%mem` | Sort by memory | Memory hogs |
| `watch 'command'` | Repeat command | Auto-refresh |
| `iotop` | I/O usage | Disk activity |
| `vmstat 1 5` | VM statistics | 5 samples, 1s apart |

## Performance Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `time command` | Measure time | Total duration |
| `perf record cmd` | Profile | CPU sampling |
| `perf report` | Show results | Analyze perf data |
| `valgrind cmd` | Memory check | Detect leaks |
| `valgrind --tool=callgrind cmd` | Call profiling | Function counts |

## Log Levels

| Level | Priority | Use |
|-------|----------|-----|
| EMERG | 0 | System unusable |
| ALERT | 1 | Action needed |
| CRIT | 2 | Critical |
| ERROR | 3 | Error |
| WARN | 4 | Warning |
| NOTICE | 5 | Significant |
| INFO | 6 | Informational |
| DEBUG | 7 | Debugging |

## Trace Filtering

| Option | Purpose | Example |
|--------|---------|---------|
| `-e trace=open` | Specific syscall | File opens |
| `-e trace=file` | Category | All file ops |
| `-e trace=network` | Network | Socket calls |
| `-e trace=process` | Process | fork, exec |
| `-c` | Count only | Statistics |
| `-v` | Verbose | More detail |

## Process States

| State | Meaning | Behavior |
|-------|---------|----------|
| R | Running | On CPU now |
| S | Sleeping | Interruptible wait |
| D | Disk sleep | Uninterruptible |
| Z | Zombie | Terminated, awaiting |
| T | Stopped | Paused (SIGSTOP) |
| t | Trace stop | Being debugged |

## Memory Profiling

| Tool | Purpose | Output |
|------|---------|--------|
| Valgrind | Leak detection | Leak report |
| Massif | Memory over time | Graph |
| Callgrind | Call counts | Call graph |
| Top | Real-time | Live monitor |
| /proc/pid | OS view | Detailed info |

## Log File Locations

| Log | Path | Contains |
|-----|------|----------|
| System | /var/log/syslog | OS events |
| Auth | /var/log/auth.log | Login attempts |
| Kernel | /var/log/kern.log | Kernel messages |
| App logs | /var/log/app | Application logs |
| Journald | Binary | systemd logs |

## Debugging Tools

| Tool | Purpose | Use |
|------|---------|-----|
| strace | Syscall trace | OS-level debug |
| ltrace | Library trace | Code-level debug |
| gdb | Debugger | Breakpoints, stepping |
| valgrind | Memory errors | Leaks, corruption |
| perf | Profiling | CPU sampling |

## Quick Monitoring

```bash
# Top processes by CPU
ps -eo pid,%cpu,cmd --sort=-%cpu | head

# Top processes by memory
ps -eo pid,%mem,rss,cmd --sort=-%mem | head

# System load
uptime

# Memory usage
free -h

# Disk I/O
iostat -x 1

# Network
ss -tuln

# Process detail
ps -eo pid,ppid,cmd
cat /proc/PID/status
```

## Observability Best Practices

```
Logs:
- Timestamps on all events
- Structured logging (JSON)
- Log rotation enabled
- Retention policy

Metrics:
- Sample interval clear
- Threshold alerts
- Baseline for comparison

Tracing:
- Trace IDs for correlation
- Sampling strategy
- Overhead monitoring
```

## Common Issues

| Issue | Symptom | Check |
|-------|---------|-------|
| Memory leak | Growing VSZ/RSS | valgrind, top |
| CPU high | Load high, %CPU high | perf, top, strace |
| Slow I/O | Disk busy, slow response | iotop, iostat |
| Network slow | High latency | traceroute, ss |
| Hanging process | No response | strace -p, lsof |

## Quick Reference

```bash
# Log monitoring
journalctl -f | grep error

# Trace overhead
strace -c command

# Find leaks
valgrind --leak-check=full program

# Profile
perf record --call-graph=dwarf program
perf report

# Monitor
watch -n 1 'top -b -n 1 -p PID'
```
