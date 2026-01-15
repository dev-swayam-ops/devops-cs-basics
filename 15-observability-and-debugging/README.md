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

Observability is the ability to understand your system from its external outputs. It's built on three complementary types of data.

**The Three Pillars of Observability**

**Logs (Events)**
- Detailed record of what happened
- When: specific timestamp
- Where: which service, component, file
- What: event message, structured data
- Examples: "User login failed", "Database connection timeout"
- Immutable: once written, cannot change
- Storage: log files, centralized systems (ELK, Splunk)
- Granularity: can be very detailed or sampling
- Performance: writing logs has overhead (I/O cost)
- Use: investigating incidents, audit trail, event history
- Challenge: log volume grows rapidly, storage expensive

**Metrics (Measurements)**
- Numerical measurements over time
- Examples: CPU usage (75%), memory (4.2GB), request rate (1000/sec)
- Time-series data: value changes over time
- Aggregated: summary of activity
- Storage: much smaller than logs
- Query: easy to query and graph
- Alerting: threshold-based (CPU > 80% = alert)
- Use: trending, capacity planning, performance baseline
- Examples: Prometheus, Graphite, Datadog
- Typical metrics: latency, throughput, error rate, resource utilization

**Traces (Requests)**
- Distributed tracing: follow request across services
- Single request can touch 5-10 different services
- Creates trace ID: unique identifier for request
- Spans: time spent in each service
- Parent-child relationships: understand service dependencies
- Latency attribution: which service is slow?
- Example: user clicks → API → database → cache → external service
- Storage: high volume but searchable
- Use: understanding slow requests, finding bottlenecks
- Tools: Jaeger, Zipkin, Datadog APM

**Why All Three?**
- Logs give details but are expensive at scale
- Metrics are cheap but lose detail
- Traces show flow but only for sampled requests
- Together: comprehensive picture of system health
- Different questions: logs (what?), metrics (how often?), traces (how slow?)

### 2. Log Levels and Severity

Logs are classified by severity to help filter and prioritize. Understanding levels prevents log noise while ensuring critical issues aren't missed.

**Standard Log Levels (Low to High Severity)**

**DEBUG**
- Developer information
- Detailed execution flow
- Variable values, loop iterations
- Not for production: too verbose
- Example: "Processing batch item 42/500"
- Enable: only when investigating issues
- Performance: can slow down system significantly

**INFO**
- Normal operational messages
- Important milestones
- Service startup, configuration loaded
- Status changes
- Example: "Server started on port 8080", "Database connection established"
- Safe for production: provides overview without noise
- Common volume: moderate

**WARN (WARNING)**
- Unexpected but recoverable situations
- Should investigate but system continues
- Examples: "Retry failed 2/3 times", "Cache miss rate high"
- Indicates problems: not immediately broken but degrading
- Action: investigate within hours
- Example: "Database response time above threshold"

**ERROR**
- Error occurred: function failed
- System continues but functionality affected
- Examples: "Failed to send email", "User not found in database"
- Action: investigate immediately
- User impact: feature broken or degraded
- Multiple errors: may indicate cascading failure

**FATAL/CRITICAL**
- System cannot continue
- Application must shut down
- Immediate attention required
- Examples: "Out of memory", "Disk full", "Database connection lost"
- Action: alert and page on-call engineer
- Recovery: restart application or manual intervention

**TRACE (Rarely Used)**
- Even more detailed than DEBUG
- Very fine-grained: every line execution
- Performance impact: severe
- Only in development: never in production

**Log Level Best Practices**
- DEBUG: development, testing
- INFO: important milestones, user actions
- WARN: degradation, retries, unusual patterns
- ERROR: failures that affect functionality
- FATAL: system-level failures
- Configurable: change level at runtime without restart
- Default: usually INFO for production

### 3. Debugging Tools and Techniques

When systems fail, debugging tools help diagnose root causes. Each tool serves a specific purpose.

**strace (System Call Tracing)**
- Traces system calls made by process
- Shows interaction between program and kernel
- Example: which files are opened? which network calls?
- Output: sys_open(), sys_read(), sys_write(), etc.
- Use: understand what program is doing at OS level
- Command: `strace command`
- Overhead: moderate, can slow process
- Shows: every file operation, network socket call, memory allocation

**ltrace (Library Call Tracing)**
- Traces library function calls
- Similar to strace but for library calls
- Examples: malloc(), printf(), strncpy()
- Use: understand what libraries program uses
- Shows: function arguments and return values
- Overhead: can be significant with frequently-called functions

**gdb (GNU Debugger)**
- Interactive debugger for running programs
- Set breakpoints: pause at specific line
- Step through: line-by-line execution
- Inspect variables: see values at runtime
- Backtrace: see call stack when crashed
- Use: investigate segfaults, logic errors, infinite loops
- Symbols needed: compile with -g flag for best results
- Modes: interactive shell or command-line

**Valgrind (Memory Debugger)**
- Detects memory leaks
- Finds invalid memory access
- Detects use-after-free bugs
- Shows: which function allocated memory not freed
- Output: memory report with line numbers
- Overhead: significant (4-20x slower)
- Use: memory profiling, leak hunting
- Languages: C, C++

**perf (Performance Profiler)**
- Analyzes CPU usage
- Shows which functions consume most CPU time
- Identifies hot paths: where time is spent
- Use: optimization, finding performance bottlenecks
- Output: flame graphs, call graphs
- Overhead: low
- Records: CPU cycles, cache misses, branch mispredictions

**Core Dumps**
- Snapshot of memory when program crashes
- Contains: all variables, call stack, state
- Useful: analyze crashes after they happened
- Enable: `ulimit -c unlimited`
- Size: can be large (GB)
- Tools: gdb can load and analyze core dumps

### 4. Metrics Types and Key Metrics

Metrics are numerical measurements. Understanding metric types helps choose right monitoring strategy.

**Gauge Metrics**
- Current value at moment
- Goes up and down
- Examples: CPU usage (0-100%), memory usage (bytes), temperature
- Can be fractional: 3.14, 99.99%
- Snapshot: shows current state
- Query: instantaneous, no history needed
- Examples: active connections, queue length, disk free space

**Counter Metrics**
- Only increases over time
- Never decreases (except reset)
- Examples: total requests served, total errors, total bytes sent
- Useful for: rates (requests per second)
- Query: rate(metric[1m]) = requests per minute
- Never query absolute value: always calculate rate
- Reset: counter resets when service restarts

**Histogram Metrics**
- Measures distribution of values
- Buckets: divides range into categories
- Examples: response time distribution, request size distribution
- Shows: what percentage of requests are fast vs slow
- Quantiles: p50 (median), p95, p99 (tail latency)
- Use: performance SLO definition (p99 < 100ms)
- Overhead: storage cost for buckets

**Summary Metrics**
- Similar to histogram but calculates quantiles
- Shows: percentiles (p50, p95, p99)
- Use: understand latency distribution
- Example: "95% of requests < 200ms, 99% < 500ms"
- Granular: more precise than histogram buckets

**Rate Metrics**
- Derived from counters
- How fast counter increases
- Examples: requests/second, errors/second, bytes/second
- Calculate: (value_now - value_before) / time_interval
- Useful for: traffic patterns, alerting

**Critical Business Metrics**
- Request rate: requests per second
- Error rate: % of requests that failed
- Latency: response time (p50, p95, p99)
- Saturation: how full is resource (CPU, memory, disk)
- Availability: % of time service responded

### 5. Distributed Tracing

Distributed tracing follows a single user request through multiple services. Essential for microservices debugging.

**Core Concepts**

**Trace ID**
- Unique identifier for entire request
- Same across all services
- Propagated: passed between services
- Example: trace-123456789
- Enables: see complete path through system

**Span**
- Time spent in single service
- Parent-child relationship: forms tree
- Example: "span-api" (parent) → "span-db" (child)
- Contains: start time, duration, status
- Tags: service name, operation, status code

**Trace Structure**
```
Trace: user request
├── Span: API server (50ms)
│   ├── Span: database query (30ms)
│   └── Span: cache check (5ms)
├── Span: Auth service (20ms)
└── Span: Payment service (15ms)
Total: ~85ms
```

**Propagation**
- Request enters service A with trace-123
- Service A adds its span
- Calls service B, passes trace-123
- Service B adds its span as child
- Service B calls database, passes trace-123
- All operations linked by trace-123

**Sampling**
- Cannot trace every request: too expensive
- Sample: trace 1% or 10% of requests
- Adaptive: sample more under high load, less under normal
- Head-based: decide at start of request
- Tail-based: decide at end of trace (better but harder)

**Benefits**
- Find bottleneck: which service is slow?
- Understand flow: request path through system
- Error tracking: see exactly where failure occurred
- Performance analysis: latency breakdown by service
- Dependency mapping: understand service interactions

**Trace Tools**
- Jaeger: open source, distributed tracing
- Zipkin: similar, good for Java
- DataDog APM: commercial, full observability
- New Relic: commercial, enterprise tracing

**Implementation**
- Instrumentation: add tracing code to services
- Library support: most frameworks have tracing
- Overhead: minimal with sampling
- Storage: trace data stored in backend
- Retention: typically 7-30 days

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

