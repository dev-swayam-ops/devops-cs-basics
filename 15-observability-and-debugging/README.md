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

### 1. Observability and the Three Pillars

**Logs**: Event records (what happened, when, where). Detailed, immutable, expensive at scale. Use: incident investigation, audit trail.

**Metrics**: Numerical measurements (CPU %, memory GB, requests/sec). Aggregated, cheap to store, easy to query and alert. Use: trending, capacity planning, baselines.

**Traces**: Request flow across services. Single trace-ID tracks one request through multiple services (API → DB → cache). Shows which service is slow. Use: microservices debugging, latency attribution.

**Together**: Logs answer "what?", metrics answer "how often?", traces answer "how slow?" and "where?". Comprehensive system observability requires all three.

### 2. Log Levels and Severity

**DEBUG**: Developer info, execution flow, variable values. Not for production (too verbose). Enable only when investigating issues.

**INFO**: Normal operational messages, important milestones (startup, config loaded). Safe for production, moderate volume. Default level.

**WARN**: Unexpected but recoverable (retries, degradation). System continues. Investigate within hours.

**ERROR**: Function failed, functionality affected. Investigate immediately. User impact or cascading failure possible.

**FATAL/CRITICAL**: System cannot continue, must shut down. Immediate action required. Page on-call engineer.

**Best Practices**: DEBUG (dev/testing), INFO (milestones), WARN (degradation), ERROR (failures), FATAL (system-level). Configurable at runtime. Default: INFO for production.

### 3. Debugging Tools and Techniques

**strace**: Traces system calls made by process. Shows OS-level interaction (file opens, network calls, memory allocation). Overhead: moderate. Command: `strace command`.

**ltrace**: Traces library function calls (malloc, printf, etc.). Shows function arguments and return values. Overhead: can be significant. Good for understanding library usage.

**gdb**: Interactive debugger. Set breakpoints, step through code, inspect variables, see call stack on crash. Compile with `-g` flag. Use for: segfaults, logic errors, infinite loops.

**Valgrind**: Memory debugger. Detects leaks, invalid access, use-after-free. Output: memory report with line numbers. Overhead: 4-20x slower. Languages: C, C++.

**perf**: CPU profiler. Shows which functions consume most CPU time, identifies hot paths. Output: flame graphs, call graphs. Overhead: low. Records: cycles, cache misses.

**Core Dumps**: Snapshot of memory at crash. Contains variables, call stack, state. Enable: `ulimit -c unlimited`. Analyze with gdb.

### 4. Metrics Types and Key Metrics

**Gauge**: Current value at moment (CPU %, memory, temperature). Goes up and down. Examples: active connections, queue length, disk free. Snapshot shows current state.

**Counter**: Only increases over time (total requests, total errors, total bytes). Never decreases (except reset). Always calculate rate: `rate(metric[1m])` = requests/minute. Resets on restart.

**Histogram**: Distribution of values across buckets. Shows what percentage of requests fast vs slow. Quantiles: p50 (median), p95, p99 (tail latency). Use for SLO definition (p99 < 100ms). Storage overhead for buckets.

**Summary**: Similar to histogram but calculates percentiles (p50, p95, p99). Example: "95% of requests < 200ms, 99% < 500ms". More precise than histogram.

**Rate Metrics**: Derived from counters. How fast counter increases. Examples: requests/sec, errors/sec, bytes/sec. Calculate: (value_now - value_before) / time_interval.

**Critical Business Metrics**: Request rate (requests/sec), Error rate (% failed), Latency (p50/p95/p99), Saturation (resource fullness), Availability (% uptime).

### 5. Distributed Tracing

**Trace ID**: Unique identifier for entire request. Same across all services. Propagated between services. Enables seeing complete path through system.

**Span**: Time spent in single service. Parent-child relationships form tree. Contains: start time, duration, status, tags (service name, operation, status code). Example: API server (50ms) → DB query (30ms) + cache check (5ms).

**Propagation**: Request enters service A with trace-123. Service A adds its span, calls service B, passes trace-123. Service B adds span as child. All operations linked by trace-123.

**Sampling**: Cannot trace every request (too expensive). Sample 1% or 10% of requests. Adaptive sampling (more under high load, less under normal). Head-based (decide at start), Tail-based (decide at end, better but harder).

**Benefits**: Find bottleneck (which service slow?), understand flow (request path), error tracking (failure location), performance analysis (latency breakdown), dependency mapping.

**Tools & Implementation**: Jaeger (open source), Zipkin (Java), Datadog APM (commercial), New Relic. Instrumentation needed. Most frameworks support tracing. Overhead: minimal with sampling. Retention: 7-30 days.

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

