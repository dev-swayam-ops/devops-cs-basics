# 15 - Observability and Debugging

## What You'll Learn

Master observability and debugging techniques:
- Logs, metrics, and traces (three pillars)
- Logging best practices
- Metrics collection and visualization
- Distributed tracing
- Debugging tools and techniques
- Performance profiling
- Root cause analysis

## Prerequisites

- Completed **14-security-basics**
- Understanding of systems and applications
- Command-line debugging experience

## Key Concepts

### 1. Three Pillars of Observability
- **Logs**: Discrete events and messages from application
- **Metrics**: Quantitative measurements (CPU, latency, errors)
- **Traces**: Distributed request flow across services

### 2. Logging Levels
- **DEBUG**: Detailed information for debugging
- **INFO**: General informational messages
- **WARN**: Warning messages, still operating
- **ERROR**: Error messages, functionality impaired
- **FATAL**: Fatal errors, system cannot continue

### 3. Metrics Types
- **Gauge**: Current value (CPU usage, memory)
- **Counter**: Only increases (total requests)
- **Histogram**: Distribution of values (request latency)
- **Summary**: Percentiles (p50, p95, p99)

### 4. Debugging Approaches
- **Print debugging**: Add log statements
- **Interactive debugging**: Step through code
- **Tracing**: Track system calls (strace)
- **Profiling**: Find performance bottlenecks

## Hands-on Lab: Observability

### Lab Steps

```bash
# 1. View application logs
tail -f /var/log/syslog
grep ERROR /var/log/syslog

# 2. Structured logging (JSON)
echo '{"level":"INFO","msg":"User login","user":"john","timestamp":"2024-01-15"}' | jq

# 3. Collect metrics with proc filesystem
cat /proc/stat
cat /proc/loadavg
cat /proc/meminfo

# 4. Top for real-time metrics
top -b -n 1 | head -20
top -p $$ -b -n 1

# 5. Vmstat for memory statistics
vmstat 1 5

# 6. Iostat for disk metrics
iostat -x 1 5

# 7. Network metrics
netstat -i
ss -s

# 8. Trace system calls
strace -e trace=open,read,write echo "test"

# 9. Profile with time
time curl http://example.com

# 10. Debug with set -x
set -x
echo "debugging"
set +x
```

## Validation

Verify your observability knowledge:

```bash
# Can you view logs?
tail /var/log/syslog && echo "✓ Log viewing works"

# Can you collect metrics?
cat /proc/loadavg && echo "✓ Metric collection works"

# Can you trace?
strace -e trace=open echo "test" && echo "✓ Tracing works"

# Can you understand pillars?
echo "✓ Observability concepts understood"
```

## Cleanup

```bash
rm -f /tmp/debug.log
unset DEBUG
```

## Common Mistakes

1. **Not enough logging**: Can't debug without logs
2. **Too much logging**: Performance hit and disk usage
3. **No structured logging**: Hard to parse and search
4. **No metric retention**: Can't see historical trends
5. **Not tracing**: Can't see full request path
6. **No alerting**: Issues not discovered until users complain
7. **Removing debug logs in prod**: Need them when things break

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't find root cause | Check: logs, traces, metrics together |
| Slow queries | Profile: query time, lock time, execution plan |
| Memory leak | Monitor: heap growth, garbage collection |
| High latency | Trace: request path, check bottlenecks |
| Silent failures | Add: logging, alerts, monitoring |

## Next Steps

1. Move to **16-interview-notes-and-cheatsheets** for synthesis
2. Learn about observability platforms (Datadog, New Relic, Splunk)
3. Study alerting and notification rules
4. Explore log aggregation (ELK, Loki)
5. Learn about SLIs, SLOs, and error budgets

## Additional Resources

- Observability: https://www.oreilly.com/library/view/observability-engineering/
- ELK Stack: https://www.elastic.co/what-is/elk-stack
- Prometheus: https://prometheus.io/
- Jaeger tracing: https://www.jaegertracing.io/

