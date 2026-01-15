# 15 - Observability and Debugging

## What You'll Learn

- Logging fundamentals and levels
- Monitoring and metrics collection
- Distributed tracing concepts
- Debugging tools (strace, ltrace, gdb)
- Log analysis and aggregation
- Performance profiling
- Root cause analysis

## Prerequisites

- Completed **14-security-basics**
- Understanding of processes and systems
- Comfort with command line

## Key Concepts

### 1. Observability Three Pillars
```
Logs: Events and messages
Metrics: Numeric measurements
Traces: Request flow across services
```

### 2. Log Levels
```
DEBUG: Detailed diagnostic info
INFO: General informational messages
WARN: Warning messages (may need attention)
ERROR: Error messages (problems)
FATAL/CRITICAL: System failure
```

### 3. Debugging Tools
- **strace**: System call tracing
- **ltrace**: Library call tracing
- **gdb**: Debugger (breakpoints, variables)
- **valgrind**: Memory profiling

### 4. Metrics Types
- **Counter**: Increasing count (requests)
- **Gauge**: Current value (memory)
- **Histogram**: Distribution (latency)
- **Rate**: Per-second (throughput)

### 5. Distributed Tracing
```
Trace ID: Track request across services
Span: Single operation
Parent-child: Service relationships
```

## Hands-on Lab: Log Monitoring and Debugging

### Lab Overview
Analyze logs, monitor processes, trace system calls.

### Lab Commands

```bash
# 1. View system logs
journalctl -n 20

# Expected: Last 20 system messages

# 2. Follow live logs
journalctl -f

# Expected: (real-time messages)

# 3. Show application logs
tail -f /var/log/syslog

# Expected: (tailing output)

# 4. Search logs
grep "error" /var/log/syslog

# Expected: (error lines)

# 5. Trace system calls
strace -e trace=open,read sleep 1

# Expected: system calls from sleep

# 6. Count system calls
strace -c sleep 1

# Expected: call statistics

# 7. Trace library calls
ltrace -e printf sleep 1

# Expected: library calls

# 8. Monitor process
top -p [PID]

# Expected: (process details)

# 9. CPU profiling
ps -eo pid,%cpu,cmd --sort=-%cpu

# Expected: (by CPU usage)

# 10. Memory debugging
ps -eo pid,vsz,rss,cmd --sort=-rss

# Expected: (by memory usage)
```

## Validation

```bash
# Can you view logs?
journalctl -n 5 && echo "✓ Logs visible"

# Trace system calls?
strace -e trace=open sleep 1 && echo "✓ Tracing works"

# Monitor processes?
ps aux && echo "✓ Monitoring works"

# Understand log levels?
echo "✓ Log levels understood"
```

## Cleanup

```bash
# No cleanup needed (read-only operations)
```

## Common Mistakes

1. **Log verbosity**: Too many = noise, too few = blind
2. **No trace IDs**: Can't correlate requests
3. **Lost logs**: No persistence in memory
4. **No timestamps**: Can't order events
5. **Sensitive data in logs**: Leaks passwords/keys

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't find logs | Check: /var/log, journalctl, app config |
| Logs too large | Use: rotation, compression, cleanup |
| Slow response | Check: strace, ltrace, profile |
| Memory leak | Use: valgrind, top, memory traces |
| High CPU | Profile: perf, top, flamegraph |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **16-interview-notes-and-cheatsheets** for final review

## Additional Resources

- Logs: `man journalctl`, `man tail`
- Tracing: `man strace`, `man ltrace`
- Profiling: perf, flamegraph, py-spy
- Observability: Prometheus, Loki, Jaeger

