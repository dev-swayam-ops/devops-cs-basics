# 01 - OS Fundamentals: Quiz

## Multiple Choice Questions (7 Questions)

### Question 1
What is the primary role of the Operating System kernel?

A) Display graphics on the monitor  
B) Manage hardware resources and provide an interface between applications and hardware  
C) Compile source code into executable programs  
D) Store user files on disk

<details>
<summary>Answer</summary>
<strong>B) Manage hardware resources and provide an interface between applications and hardware</strong>

The kernel is the core of the OS that manages CPU, memory, I/O devices, and provides system calls for applications to request hardware services.
</details>

---

### Question 2
In Linux file permissions represented as `-rw-r--r--`, what do the characters at positions 2-4 represent?

A) Group permissions  
B) Owner permissions  
C) Other user permissions  
D) File type

<details>
<summary>Answer</summary>
<strong>B) Owner permissions</strong>

The first character is file type (-), positions 2-4 (rw-) are owner permissions, 5-7 are group, and 8-10 are others. This shows owner can read and write, but not execute.
</details>

---

### Question 3
What does the `uptime` command primarily show?

A) The current date and time  
B) How long the system has been running and the load average  
C) CPU and disk usage statistics  
D) The version of the kernel

<details>
<summary>Answer</summary>
<strong>B) How long the system has been running and the load average</strong>

`uptime` displays system uptime and load averages (1-min, 5-min, 15-min). It's a quick way to check system health and how many users are logged in.
</details>

---

### Question 4
On a 4-CPU system, if the load average is 8.0, what does this indicate?

A) The system has 8 CPUs  
B) The system is under stress with processes waiting for CPU time  
C) The system has 8 gigabytes of RAM  
D) 8 users are currently logged in

<details>
<summary>Answer</summary>
<strong>B) The system is under stress with processes waiting for CPU time</strong>

Load of 8.0 on a 4-CPU system means 200% CPU utilization - 4 processes running + 4 waiting. This indicates the system is overloaded.
</details>

---

### Question 5
What is a system call?

A) A function that runs in user space without kernel involvement  
B) A request from a user-level process to the kernel to perform a privileged operation  
C) A command you type in the shell  
D) A program that manages user authentication

<details>
<summary>Answer</summary>
<strong>B) A request from a user-level process to the kernel to perform a privileged operation</strong>

System calls provide a controlled interface for user programs to request kernel services like file I/O, memory allocation, and process creation.
</details>

---

### Question 6
Which command shows memory usage with human-readable format (GB, MB)?

A) `df -h`  
B) `ps aux`  
C) `free -h`  
D) `uptime`

<details>
<summary>Answer</summary>
<strong>C) `free -h`</strong>

`free -h` displays memory usage in human-readable format (Ki, Mi, Gi). `df -h` is for disk space, `ps aux` for processes, and `uptime` for uptime/load.
</details>

---

### Question 7
What is the UID of the root user in Linux?

A) 1  
B) 0  
C) 1000  
D) 500

<details>
<summary>Answer</summary>
<strong>B) 0</strong>

The root user (superuser) always has UID 0. Regular users typically have UIDs starting from 1000. UID 0 has full system privileges.
</details>

---

## Short Answer Questions (3 Questions)

### Question 8
Explain the difference between kernel mode and user mode. Why is this distinction important?

<details>
<summary>Answer</summary>

**Kernel Mode:**
- Full access to all hardware (CPU, memory, I/O devices)
- Privileged instructions available
- Any operation can be performed

**User Mode:**
- Restricted hardware access
- Cannot directly access hardware
- Must use system calls to request kernel services

**Why Important:**
This separation provides security and stability:
- Prevents user programs from crashing the entire system
- Protects one user's data from other users' programs
- Malicious code cannot directly damage hardware
- Allows controlled resource sharing among multiple processes
</details>

---

### Question 9
If you run `ls -l /etc/shadow` and see the permissions as `-rw-------`, what does this mean and why would the file be protected this way?

<details>
<summary>Answer</summary>

**Permission Breakdown:**
- `-rw-------` = Regular file, owner can read and write, group has no access, others have no access
- Octal notation: 600

**Why Protected This Way:**
The `/etc/shadow` file contains hashed passwords for all users. It must be protected because:
- Only root should read password hashes
- Users could be compromised if password hashes were publicly visible
- Regular users have no legitimate need to access this file
- This prevents unauthorized access to user credentials

**Note:** This file is only readable by root (UID 0). Even if you try `cat /etc/shadow` as a regular user, you get "Permission denied".
</details>

---

### Question 10
Your server shows: `free -h` output as "Swap: 2.0Gi used: 1.5Gi". What does this indicate, and what action would you take?

<details>
<summary>Answer</summary>

**What It Indicates:**
- 1.5 GB of 2.0 GB swap space is being used (75% swap usage)
- This means physical RAM is full and the OS is using disk space as virtual memory
- System performance is severely degraded (disk access is 1000x slower than RAM)

**What Action to Take:**
1. **Immediate Investigation:**
   - Run `ps aux --sort=-%mem` to find memory-hungry processes
   - Check if any process is consuming abnormal memory
   - Review application logs for memory leaks

2. **Short-term:**
   - Stop unnecessary services
   - Restart the problematic process
   - Kill memory leaks if found

3. **Long-term:**
   - Increase physical RAM
   - Optimize application to use less memory
   - Set up memory monitoring/alerts to catch this earlier

**Alert Threshold:** Most DevOps teams set alerts when swap usage > 50%
</details>

---

## Answer Key Summary

| Question | Answer |
|----------|--------|
| 1 | B |
| 2 | B |
| 3 | B |
| 4 | B |
| 5 | B |
| 6 | C |
| 7 | B |
| 8 | See detailed answer above |
| 9 | See detailed answer above |
| 10 | See detailed answer above |

---

## Scoring Guide

- **9-10 correct**: Excellent understanding of OS fundamentals
- **7-8 correct**: Good grasp of core concepts
- **5-6 correct**: Adequate knowledge, review weak areas
- **Below 5**: Study the material again, focus on system calls and permissions

---
