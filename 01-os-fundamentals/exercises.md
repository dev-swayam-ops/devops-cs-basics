# 01 - OS Fundamentals: Exercises

## Exercise 1: Identify Your OS and Kernel Version (Easy)

Write commands to find out:
- Your operating system name
- Your kernel version
- Your processor architecture (32-bit or 64-bit)

**Hint**: Check the `uname` command with different flags.

---

## Exercise 2: Explore the Linux Directory Structure (Easy)

Navigate to the root directory (`/`) and list all directories. Then answer:
1. What is the purpose of `/bin`?
2. What is the purpose of `/home`?
3. What is the purpose of `/etc`?

**Hint**: Use `cd /` and `ls -la`, then check `man hier` for directory purposes.

---

## Exercise 3: Check System Resources (Easy)

Check and document:
1. Total RAM available on your system
2. Used memory
3. Free memory
4. Total disk space
5. Available disk space

**Hint**: Use `free -h` and `df -h` commands.

---

## Exercise 4: User and Group Information (Easy)

Run the following commands and explain the output:
1. `whoami`
2. `id`
3. `groups`

Then, find out:
- What is your user ID (UID)?
- What is your group ID (GID)?
- How many groups do you belong to?

---

## Exercise 5: File Permissions Understanding (Easy)

Run `ls -l` on a few files in `/etc` directory and interpret one file's permissions:

```bash
ls -l /etc/passwd
```

For the output line, identify:
1. File type (regular file, directory, symbolic link)
2. Owner permissions
3. Group permissions
4. Other permissions
5. Owner name
6. Group name

---

## Exercise 6: Uptime and Load Average (Medium)

Run `uptime` command and explain:
1. How long the system has been running
2. What the load average means
3. Why it's important for DevOps

Then check:
- What is your current system load?
- How many CPUs does your system have? (use `nproc`)
- Is your system under stress?

---

## Exercise 7: Process Information (Medium)

Use the `ps` command to:
1. List all currently running processes
2. Find processes running as root
3. Count total number of processes

```bash
ps aux
ps aux | grep root
ps aux | wc -l
```

For the root processes, answer:
- How many processes are running as root?
- Why might certain processes need root privileges?

---

## Exercise 8: Virtual Memory and Swap (Medium)

Check your swap space usage:

```bash
free -h
swapon --show
```

Then answer:
1. How much swap space do you have?
2. How much swap is being used?
3. What is swap space and why do we need it?
4. What happens when swap space is full?

---

## Exercise 9: System Information Gathering (Medium)

Create a comprehensive system report by running these commands and saving output to a file:

```bash
echo "=== System Info ===" > system_report.txt
uname -a >> system_report.txt
echo "=== CPU Info ===" >> system_report.txt
lscpu >> system_report.txt
echo "=== Memory Info ===" >> system_report.txt
free -h >> system_report.txt
echo "=== Disk Info ===" >> system_report.txt
df -h >> system_report.txt
```

Then:
1. Display the contents of your report
2. What information would be most important for a DevOps engineer?
3. How could you use this information for capacity planning?

---

## Exercise 10: Understanding System Calls and Kernel Mode (Medium)

Research and document:
1. What is a system call?
2. Provide 3 examples of system calls
3. Why do processes need to make system calls?
4. What happens when a process makes a system call?
5. Create a simple scenario: A user starts a process that needs to read a file. Describe the flow from user mode to kernel mode.

**Hint**: Check `/proc/[PID]/syscall` for running process system calls, and research `strace` command.

---
