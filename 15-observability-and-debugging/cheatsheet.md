# 15 - Observability and Debugging: Cheatsheet

## Three Pillars of Observability

| Pillar | What | Tools |
|--------|------|-------|
| **Logs** | Event records | ELK, Splunk, Datadog |
| **Metrics** | Numeric measurements | Prometheus, Grafana |
| **Traces** | Request paths | Jaeger, Zipkin, Datadog |

## Logging Best Practices

```
Levels:
- DEBUG: Detailed info for debugging
- INFO: Important lifecycle events
- WARNING: Something unexpected
- ERROR: Error that needs attention
- CRITICAL: System failure

Good Log Format:
timestamp | level | service | message | context
2024-01-15T10:30:45 | ERROR | api | Database connection failed | db=postgres, host=db.prod
```

## Metrics to Track

```
System:
- CPU usage
- Memory usage
- Disk usage
- Network bandwidth

Application:
- Request latency (p50, p95, p99)
- Error rate
- Throughput
- Cache hit ratio

Business:
- User count
- Transaction volume
- Revenue metrics
```

## Debugging Tools

```bash
# System debugging
strace -f ./program              # System calls
ltrace ./program                 # Library calls
perf top                         # CPU hotspots
gdb ./program                    # Debugger

# Network debugging
tcpdump -i eth0 -w capture.pcap  # Packet capture
wireshark capture.pcap           # Analyze packets
netstat -an                      # Connection stats
ss -tlnp                         # Socket stats

# Log analysis
tail -f /var/log/app.log
grep ERROR /var/log/app.log | wc -l
jq . < logs.json                # Parse JSON logs
```

## Alerting Strategy

```
Alert Rules:
- Threshold-based: metric > limit
- Anomaly detection: deviation from normal
- Composite: multiple conditions

Good Alert:
- Actionable (clear what to do)
- Low false-positive rate
- Appropriate severity
- Escalation path

Bad Alert:
- Too frequent
- Not actionable
- Unclear root cause
```

## Monitoring Stack Example

```
App → Prometheus (metrics) ↓
      ↓
   Grafana (dashboards)
   Alertmanager (routing)
   
App → ELK Stack (logs)
      ↓
   Kibana (search/visualize)
   
App → Jaeger (traces)
      ↓
   UI (distributed tracing)
```

---
