# DevOps CS Basics - Complete Learning Curriculum

A comprehensive, hands-on learning curriculum designed to teach computer science fundamentals essential for DevOps engineers, system administrators, and infrastructure engineers.

## 📚 What This Repository Contains

This is a **16-module learning path** covering everything from operating system fundamentals to distributed systems, database design, and security. Each module includes:

- **📖 Detailed README** with concepts and theory
- **💪 10 Hands-on Exercises** (easy to medium difficulty)
- **✅ Complete Solutions** with explanations
- **📋 Quick Reference Cheatsheet** with commands and tools
- **🎯 Practice Quiz** with multiple-choice and short-answer questions
- **🔧 Real-world labs** with actual Linux commands

## 🎯 Who Should Use This?

- **DevOps Engineers**: Building your foundation
- **System Administrators**: Understanding system internals
- **Infrastructure Engineers**: Designing scalable systems
- **Backend Developers**: Learning systems thinking
- **Interview Candidates**: Preparing for technical interviews
- **Self-learners**: Mastering CS fundamentals

## ⏱️ Estimated Learning Time

- **Total curriculum**: 40-60 hours
- **Per module**: 2.5-4 hours
- **With deep-dives**: 60-80 hours
- **Self-paced**: Learn at your own speed

## 📋 Curriculum Overview

### Foundation (Modules 01-05)
Build strong foundational knowledge of operating systems and storage.

| # | Module | Focus Area | Prerequisites |
|---|--------|-----------|---------------|
| 01 | **[OS Fundamentals](01-os-fundamentals/)** | Kernel, processes, system resources | None |
| 02 | **[Process Management](02-process-management/)** | Process lifecycle, signals, management | Module 01 |
| 03 | **[Threads & Concurrency](03-threads-and-concurrency/)** | Synchronization, race conditions, deadlock | Module 02 |
| 04 | **[Memory Management](04-memory-management/)** | Virtual memory, paging, allocation | Module 03 |
| 05 | **[Storage & Filesystems](05-storage-and-filesystems/)** | Inodes, RAID, backups, filesystems | Module 04 |

### Infrastructure (Modules 06-10)
Understand networking and performance optimization.

| # | Module | Focus Area | Prerequisites |
|---|--------|-----------|---------------|
| 06 | **[Networking Fundamentals](06-networking-fundamentals/)** | OSI model, TCP/IP, protocols | Module 05 |
| 07 | **[DNS & HTTP Basics](07-dns-and-http-basics/)** | DNS resolution, HTTP methods | Module 06 |
| 08 | **[Linux Kernel Basics](08-linux-kernel-basics/)** | Kernel architecture, modules, syscalls | Module 07 |
| 09 | **[System Calls & Signals](09-system-calls-and-signals/)** | Syscall interface, signal handling | Module 08 |
| 10 | **[Scheduling & Performance](10-scheduling-and-performance/)** | CPU scheduling, load, profiling | Module 09 |

### Advanced Systems (Modules 11-15)
Master distributed systems and production considerations.

| # | Module | Focus Area | Prerequisites |
|---|--------|-----------|---------------|
| 11 | **[Distributed Systems Basics](11-distributed-systems-basics/)** | CAP, consensus, replication | Module 10 |
| 12 | **[Databases for DevOps](12-databases-for-devops/)** | SQL/NoSQL, indexing, replication | Module 11 |
| 13 | **[Caching & Queues](13-caching-and-queues/)** | Cache strategies, message queues | Module 12 |
| 14 | **[Security Basics](14-security-basics/)** | Encryption, SSH, HTTPS, auth | Module 13 |
| 15 | **[Observability & Debugging](15-observability-and-debugging/)** | Logs, metrics, traces, profiling | Module 14 |

### Synthesis (Module 16)
Prepare for interviews and real-world application.

| # | Module | Focus Area | Prerequisites |
|---|--------|-----------|---------------|
| 16 | **[Interview Notes & Cheatsheets](16-interview-notes-and-cheatsheets/)** | System design, interview prep | All 01-15 |

## 🚀 Getting Started

### Quick Start (5 minutes)

1. **Clone or download** this repository
2. **Start with Module 01**: [OS Fundamentals](01-os-fundamentals/)
3. **Read the README** to understand concepts
4. **Run the hands-on lab** exercises
5. **Check the solutions** and cheatsheet
6. **Take the quiz** to validate learning

### Learning Path Options

#### Option 1: Complete Path (All 16 Modules)
- **Best for**: Comprehensive understanding, interview prep
- **Time**: 40-60 hours
- **Outcome**: Deep expertise in all areas

```
01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10 → 11 → 12 → 13 → 14 → 15 → 16
```

#### Option 2: DevOps Focus (Modules 01-15)
- **Best for**: DevOps engineers and operators
- **Time**: 35-50 hours
- **Modules**: Foundation + Infrastructure + Advanced

#### Option 3: Systems Foundation (Modules 01-10)
- **Best for**: Beginner system administrators
- **Time**: 20-30 hours
- **Modules**: Foundation + Infrastructure

#### Option 4: Interview Preparation (Focus on 16 + Review)
- **Best for**: Job candidates
- **Time**: 15-25 hours
- **Approach**: Module 16 + deep-dive weak areas

#### Option 5: Self-Paced Deep-Dives
- **Best for**: Specific skill gaps
- **Time**: Variable
- **Approach**: Pick modules relevant to your role

## 📖 How to Use Each Module

### 1. Read the README
- Learn key concepts and theory
- Understand prerequisites
- Review troubleshooting guide

```bash
# Example: Read Module 01
cat 01-os-fundamentals/README.md
```

### 2. Do the Exercises
- 10 hands-on exercises per module
- Start with easy, progress to medium
- Real Linux commands you can run

```bash
# Example: View exercises
cat 01-os-fundamentals/exercises.md
```

### 3. Check Solutions
- Complete explanations for each exercise
- Learn from examples
- Understand command details

```bash
# Example: Review solutions
cat 01-os-fundamentals/solutions.md
```

### 4. Use the Cheatsheet
- Quick reference for commands
- Useful for review and practice
- Keep open while learning

```bash
# Example: Quick lookup
grep "memory" 04-memory-management/cheatsheet.md
```

### 5. Take the Quiz
- Self-assess your knowledge
- 7 multiple-choice questions
- 3 short-answer questions
- Answer key provided

```bash
# Example: Test yourself
cat 01-os-fundamentals/quiz.md
```

## 📁 Directory Structure

```
devops-cs-basics/
├── README.md                           # This file
├── README_SUMMARY.md                   # Content overview
├── QUICK_START_GUIDE.md               # Beginner guide
├── LEARNING_CONTENT_SUMMARY.md        # Detailed curriculum
│
├── 01-os-fundamentals/
│   ├── README.md                      # Concepts & theory
│   ├── exercises.md                   # 10 exercises
│   ├── solutions.md                   # Solutions & explanations
│   ├── cheatsheet.md                  # Command reference
│   └── quiz.md                        # Self-assessment
│
├── 02-process-management/
│   ├── README.md
│   ├── exercises.md
│   ├── solutions.md
│   ├── cheatsheet.md
│   └── quiz.md
│
├── ... (modules 03-15 with same structure)
│
└── 16-interview-notes-and-cheatsheets/
    ├── README.md                      # Interview prep guide
    ├── exercises.md
    ├── solutions.md
    └── cheatsheet.md                  # Synthesis of all concepts
```

## 🎓 Learning Features

### ✅ Each Module Includes
- Clear learning objectives
- Real-world DevOps context
- Practical command examples
- Expected output samples
- Common mistakes highlighted
- Troubleshooting guides
- Links to additional resources
- Progressive difficulty

### 💡 Key Principles
- **Hands-on learning**: Type commands, see results
- **Beginner-friendly**: Explained in simple language
- **Real-world focus**: DevOps relevant examples
- **Progressive complexity**: Simple to advanced
- **Self-paced**: Learn when you want
- **Comprehensive**: 160+ exercises, 70+ quizzes, 1000+ commands

## 🛠️ Tools You'll Learn

### System Exploration
- `uname`, `uptime`, `lscpu`, `free`, `df`, `ps`, `top`, `htop`

### Process Management
- `ps`, `kill`, `jobs`, `fg`, `bg`, `nice`, `renice`, `pkill`

### Networking
- `ip`, `ping`, `traceroute`, `netstat`, `ss`, `nslookup`, `dig`, `curl`

### Performance Profiling
- `perf`, `strace`, `taskset`, `iostat`, `vmstat`, `dstat`, `iftop`

### Debugging & Tracing
- `gdb`, `strace`, `ltrace`, `systemtap`, `bpftrace`

### System Administration
- `systemctl`, `journalctl`, `sysctl`, `cgroups`, `iptables`

### Database Tools
- `mysql`, `redis-cli`, `psql`, `mongo`

### Monitoring
- Logs (`tail`, `grep`, `journalctl`)
- Metrics (`prometheus`, `grafana`)
- Traces (`jaeger`, `zipkin`)

## 📚 Knowledge Areas Covered

### Core Systems
- Operating system architecture
- Process and thread management
- Memory management and virtual memory
- Filesystem organization
- Device I/O and interrupts

### Networking
- OSI model and TCP/IP stack
- DNS resolution and HTTP protocol
- Network configuration and troubleshooting
- Performance optimization
- Security and firewalls

### Distributed Systems
- CAP theorem and consistency models
- Consensus algorithms (Raft, Paxos)
- Replication and sharding
- Failure detection and recovery
- Data consistency guarantees

### Databases
- Relational vs NoSQL
- Indexing and query optimization
- Transactions and ACID properties
- Replication and backup strategies
- Connection management

### Production Systems
- Caching strategies and invalidation
- Message queues and pub/sub
- Security (authentication, encryption)
- Observability (logs, metrics, traces)
- Performance profiling and optimization

## 🎯 Learning Outcomes

After completing this curriculum, you will:

✅ **Understand** computer science fundamentals  
✅ **Explain** how operating systems work  
✅ **Design** scalable distributed systems  
✅ **Optimize** application and system performance  
✅ **Debug** complex system issues  
✅ **Manage** databases and data storage  
✅ **Secure** systems and communications  
✅ **Monitor** production systems  
✅ **Pass** technical interviews  
✅ **Build** reliable infrastructure  

## 🧪 Prerequisites

### Required
- Linux command line basics
- Text editor experience (nano, vim, or VS Code)
- Basic familiarity with terminals

### Helpful
- Some programming experience (bash, Python, or similar)
- Understanding of networking (IP addresses, ports)
- General systems thinking

### No Prerequisites Needed
- Advanced programming
- Specific framework knowledge
- Professional experience

## 💻 System Requirements

- **OS**: Linux (Ubuntu, Fedora, Debian, etc.) or macOS
- **Disk space**: 500MB (for tools and labs)
- **Memory**: 2GB RAM minimum
- **Tools**: Installed on most Linux systems (free)

### Optional Tools (Free)
- Docker (for containerized labs)
- VirtualBox (for isolated environments)
- VS Code (for code editing)

## 📖 Recommended Study Schedule

### Aggressive (40 hours - 2 weeks)
```
Mon-Fri: 2-3 hours/day
Sat-Sun: 2 hours/day
Complete 2-3 modules per week
```

### Moderate (50 hours - 4 weeks)
```
Mon-Fri: 1.5-2 hours/day
Sat-Sun: 1 hour/day
Complete 1-2 modules per week
```

### Relaxed (60+ hours - 8 weeks)
```
Mon-Fri: 1 hour/day
Sat-Sun: 1-2 hours/day
Complete 1 module per week
```

## 🚨 Common Questions

### Q: Do I need to be a programmer?
**A:** No! This is about systems, not coding. Basic bash scripts are included, but you don't need to write code.

### Q: Can I skip modules?
**A:** Not recommended. Each module builds on previous ones. Start with Module 01.

### Q: How do I practice?
**A:** Run every command in the hands-on labs. Type them yourself, don't copy-paste.

### Q: Can I use this on macOS?
**A:** Most commands work on macOS. Some (like systemd) are Linux-specific.

### Q: I'm stuck on an exercise. What should I do?
**A:** 1. Re-read the README for that concept  
2. Check the troubleshooting guide  
3. Review the solution  
4. Try a simpler version first

### Q: How does this help with job interviews?
**A:** Module 16 has interview prep. Understanding these fundamentals helps you think through system design problems.

### Q: Is this free?
**A:** Yes! This entire curriculum is free and open-source.

## 🤝 Contributing

Found a typo? Want to add content? Improvements welcome!

- Report issues via GitHub
- Submit pull requests with improvements
- Share this with others learning DevOps

## 📞 Support & Community

- **Issues**: Found a problem? Open an issue
- **Discussions**: Have questions? Use discussions
- **Reddit**: r/devops, r/linux
- **Discord**: DevOps communities
- **Local meetups**: Find DevOps groups near you

## 🔗 Additional Resources

### Official Documentation
- [Linux kernel docs](https://www.kernel.org/doc/)
- [GNU/Linux man pages](https://man7.org/linux/man-pages/)
- [Linux Foundation](https://www.linuxfoundation.org/)

### Books
- "Operating Systems: Three Easy Pieces"
- "Designing Data-Intensive Applications"
- "The Linux Programming Interface"
- "Systems Performance"

### Online Platforms
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [LeetCode](https://leetcode.com/)
- [InterviewBit](https://www.interviewbit.com/)

### Communities
- [DevOps subreddit](https://www.reddit.com/r/devops/)
- [Linux foundation events](https://www.linuxfoundation.org/events/)
- [GitHub discussions](https://github.com/)

## 📋 Progress Tracker

Track your progress through the curriculum:

```
Foundation (Modules 01-05)
[ ] 01 - OS Fundamentals
[ ] 02 - Process Management
[ ] 03 - Threads & Concurrency
[ ] 04 - Memory Management
[ ] 05 - Storage & Filesystems

Infrastructure (Modules 06-10)
[ ] 06 - Networking Fundamentals
[ ] 07 - DNS & HTTP Basics
[ ] 08 - Linux Kernel Basics
[ ] 09 - System Calls & Signals
[ ] 10 - Scheduling & Performance

Advanced (Modules 11-15)
[ ] 11 - Distributed Systems Basics
[ ] 12 - Databases for DevOps
[ ] 13 - Caching & Queues
[ ] 14 - Security Basics
[ ] 15 - Observability & Debugging

Synthesis
[ ] 16 - Interview Notes & Cheatsheets
```

## 📝 License

This curriculum is open-source and free to use, modify, and share.

## 🎉 Let's Get Started!

Ready to master DevOps fundamentals? 

**Start here:** [Module 01 - OS Fundamentals →](01-os-fundamentals/README.md)

Or read the [Quick Start Guide](QUICK_START_GUIDE.md) for beginner guidance.

---

**Last Updated**: January 2026  
**Status**: Complete (16 modules, 160+ exercises, 70+ quizzes)  
**Difficulty**: Beginner to Intermediate

## 📚 Learning Modules

### Foundation Tier (OS & System Basics)
These modules provide the essential foundation for everything else:

| # | Module | Focus | Status |
|---|--------|-------|--------|
| 1 | [OS Fundamentals](01-os-fundamentals/) | Kernel, processes, system resources | Core |
| 2 | [Process Management](02-process-management/) | Process lifecycle, fork/exec, signals | Core |
| 3 | [Threads & Concurrency](03-threads-and-concurrency/) | Threading, synchronization, race conditions | Core |
| 4 | [Memory Management](04-memory-management/) | Virtual memory, paging, heap/stack | Core |
| 5 | [Storage & Filesystems](05-storage-and-filesystems/) | Disk I/O, file systems, RAID, storage layers | Core |

### Intermediate Tier (System Operations)
Build on foundation concepts to understand system-level operations:

| # | Module | Focus | Status |
|---|--------|-------|--------|
| 6 | [Networking Fundamentals](06-networking-fundamentals/) | TCP/IP, packets, protocols, OSI model | Core |
| 7 | [DNS & HTTP Basics](07-dns-and-http-basics/) | DNS resolution, HTTP/HTTPS, web protocols | Core |
| 8 | [Linux Kernel Basics](08-linux-kernel-basics/) | Kernel architecture, modules, system calls | Core |
| 9 | [System Calls & Signals](09-system-calls-and-signals/) | System call interface, signal handling | Core |
| 10 | [Scheduling & Performance](10-scheduling-and-performance/) | CPU scheduling, performance tuning, profiling | Core |

### Advanced Tier (Distributed Systems & DevOps)
Apply foundational knowledge to distributed systems and infrastructure:

| # | Module | Focus | Status |
|---|--------|-------|--------|
| 11 | [Distributed Systems Basics](11-distributed-systems-basics/) | CAP theorem, consensus, eventual consistency | Reference |
| 12 | [Databases for DevOps](12-databases-for-devops/) | Relational DBs, NoSQL, replication, backups | Reference |
| 13 | [Caching & Queues](13-caching-and-queues/) | Redis, message queues, cache strategies | Reference |
| 14 | [Security Basics](14-security-basics/) | Authentication, encryption, SSL/TLS, firewalls | Reference |
| 15 | [Observability & Debugging](15-observability-and-debugging/) | Logging, metrics, tracing, debugging | Reference |

### Bonus Resources
| # | Module | Focus |
|---|--------|-------|
| 16 | [Interview Notes & Cheatsheets](16-interview-notes-and-cheatsheets/) | Quick reference for interviews and system design |

## 🚀 How to Use This Repository

### For Beginners
1. Start with **Module 1: OS Fundamentals** - understand the basics
2. Progress sequentially through Module 2-5 (Foundation Tier)
3. Move to Modules 6-10 (Intermediate Tier)
4. Reference Advanced Tier modules as needed

### For Practicing
Each module follows this workflow:

```
1. Read README.md → Understand concepts
   ↓
2. Review cheatsheet.md → Get quick reference
   ↓
3. Attempt exercises.md → Practice hands-on
   ↓
4. Check solutions.md → Compare your approach
   ↓
5. Take quiz.md → Self-assess understanding
```

### For Quick Reference
- Visit [Interview Notes & Cheatsheets](16-interview-notes-and-cheatsheets/) for a consolidated reference
- Use individual cheatsheets within each module for detailed quick lookups

## 📖 Module Structure

Each module (1-10) is fully equipped with:

```
module-folder/
├── README.md          # Comprehensive guide with key concepts and labs
├── cheatsheet.md      # Quick reference of important terms and commands
├── exercises.md       # Hands-on practice problems
├── solutions.md       # Detailed solutions and explanations
└── quiz.md           # Self-assessment questions
```

Advanced modules (11-15) include README and cheatsheet as reference materials.

## 🎓 Learning Outcomes

By completing this curriculum, you will:

- ✅ Understand how operating systems manage resources
- ✅ Master process management and concurrency concepts
- ✅ Design and debug complex distributed systems
- ✅ Work effectively with databases and storage systems
- ✅ Implement monitoring and observability solutions
- ✅ Secure systems and handle authentication/encryption
- ✅ Optimize system performance
- ✅ Ace technical interviews with deep CS fundamentals

## 💡 Best Practices

### While Learning
- **Don't skip the foundation** - Modules 1-5 are essential for understanding everything else
- **Hands-on is key** - Always attempt exercises before looking at solutions
- **Take notes** - Use cheatsheets to consolidate your learning
- **Revisit concepts** - Return to modules when you forget details

### For DevOps Application
- Apply OS fundamentals when debugging container issues
- Use networking knowledge to troubleshoot microservices
- Reference distributed systems when designing infrastructure
- Use security concepts in your deployment pipelines
- Apply observability learnings to monitoring solutions

## 🔗 Prerequisites

Before starting:
- Comfortable with Linux command line (bash/sh)
- Basic understanding of programming concepts (variables, loops, functions)
- Willingness to experiment and debug
- Text editor proficiency (nano, vim, or VS Code)

**No advanced programming experience needed!**

## 🛠️ Tools You'll Need

- Linux/Unix environment (WSL2, Mac, or native Linux)
- Text editor (nano, vim, VS Code)
- Bash shell
- Git (to clone this repository)
- Optional: Docker, Python for some advanced exercises

## 📝 Sample Your First Module

Not sure if this is for you? Try Module 1:

1. Read [01-os-fundamentals/README.md](01-os-fundamentals/README.md)
2. Try the hands-on labs section
3. Take a look at [01-os-fundamentals/cheatsheet.md](01-os-fundamentals/cheatsheet.md)
4. Attempt [01-os-fundamentals/exercises.md](01-os-fundamentals/exercises.md)

Takes about 2-3 hours to get through the first module comfortably.

## ⏱️ Time Estimates

- **Foundation Tier (Modules 1-5)**: 40-60 hours
- **Intermediate Tier (Modules 6-10)**: 30-50 hours
- **Advanced Tier (Modules 11-15)**: 20-30 hours (as needed)
- **Total Comprehensive Path**: 90-140 hours

*Times vary based on your background and hands-on practice depth.*

## 🤝 Contributing

Found an issue? Have improvements? Contributions welcome!
- Fix typos or clarify explanations
- Add new exercises or solutions
- Improve code examples
- Suggest additional topics

## 📖 Additional Resources

While working through these modules, you may find these resources helpful:

- **Linux man pages** - `man command` in your terminal
- **The Linux Programming Interface** - Comprehensive reference book
- **Operating Systems: Three Easy Pieces** - Free online textbook
- **System Design Interview** - For advanced distributed systems

## 📅 Suggested Learning Schedule

- **Intensive**: 3-4 weeks (20-30 hours/week)
- **Regular**: 3-4 months (8-10 hours/week)
- **Leisurely**: 6-12 months (3-5 hours/week)

Pick a pace that fits your schedule and stick with it!

## ❓ FAQ

**Q: Do I need to do all modules?**
A: No! Focus on Modules 1-10 first (they build on each other). Use 11-15 as reference when needed.

**Q: Can I jump to a specific module?**
A: While possible, it's not recommended. Each module assumes knowledge from previous ones.

**Q: How hands-on are the exercises?**
A: Very! Most exercises involve writing commands, scripts, or analyzing system behavior.

**Q: Can I use these materials for teaching?**
A: Yes! These materials are designed to be educational and reusable.

**Q: What if I get stuck?**
A: Check the solutions first to understand the approach, then try again independently.

---

## 🎯 Ready to Start?

Begin with [Module 1: OS Fundamentals](01-os-fundamentals/) and follow the suggested learning path!

**Happy Learning! 🚀**

---

*Last Updated: January 2026*
*For questions or suggestions, engage with the learning materials and revisit concepts as needed.*
