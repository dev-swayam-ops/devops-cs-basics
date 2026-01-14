# 02 - Process Management: Quiz

## Multiple Choice Questions (7 Questions)

### Question 1
What is the primary difference between a program and a process?

A) A program is running code, a process is stored on disk  
B) A program is a static file on disk, a process is a running instance in memory  
C) They are the same thing  
D) A program requires more resources than a process

<details>
<summary>Answer</summary>
<strong>B) A program is a static file on disk, a process is a running instance in memory</strong>

A program is executable code stored on disk. A process is the runtime execution of that program with its own memory space, file descriptors, and system resources.
</details>

---

### Question 2
Which signal cannot be caught or ignored by a process?

A) SIGTERM  
B) SIGINT  
C) SIGKILL  
D) SIGUSR1

<details>
<summary>Answer</summary>
<strong>C) SIGKILL</strong>

SIGKILL (signal 9) is specifically designed to force termination and cannot be caught, blocked, or ignored by any process. All other signals can be trapped or ignored.
</details>

---

### Question 3
What does Ctrl+Z do to a running process?

A) Terminates the process  
B) Sends SIGKILL  
C) Suspends (stops) the process without terminating  
D) Sends SIGTERM

<details>
<summary>Answer</summary>
<strong>C) Suspends (stops) the process without terminating</strong>

Ctrl+Z sends SIGTSTP (signal 20), which suspends the process. The process can be resumed in foreground (fg) or background (bg) with the `bg` and `fg` commands.
</details>

---

### Question 4
What is the relationship between PPID and process hierarchy?

A) PPID is the process's ID number  
B) PPID is the parent process ID; it shows which process created this one  
C) PPID means "Process Priority Indicator"  
D) PPID is always the same as PID

<details>
<summary>Answer</summary>
<strong>B) PPID is the parent process ID; it shows which process created this one</strong>

PPID (Parent Process ID) identifies which process created the current process. This creates a hierarchy: init (PID 1) is the root parent of all processes.
</details>

---

### Question 5
What is a zombie process?

A) A process using 100% CPU  
B) A process that has exited but whose parent hasn't reaped its exit status  
C) A process running with SIGKILL  
D) A process with no memory allocated

<details>
<summary>Answer</summary>
<strong>B) A process that has exited but whose parent hasn't reaped its exit status</strong>

A zombie process has terminated but remains in the process table because the parent hasn't called `wait()` to read its exit status. The parent must clean up after its children.
</details>

---

### Question 6
What does the default `kill` command send to a process?

A) SIGKILL  
B) SIGSTOP  
C) SIGTERM  
D) SIGINT

<details>
<summary>Answer</summary>
<strong>C) SIGTERM</strong>

The `kill` command defaults to SIGTERM (signal 15) which requests graceful termination. The process can catch this and clean up before exiting. Use `kill -9` for SIGKILL if needed.
</details>

---

### Question 7
What is the purpose of the `nohup` command?

A) Kill a process  
B) Run a process immune to hangup signals (survives logout)  
C) Check if a process exists  
D) Display nice value

<details>
<summary>Answer</summary>
<strong>B) Run a process immune to hangup signals (survives logout)</strong>

`nohup` (no hangup) runs a command immune to SIGHUP, allowing it to continue running even after the user logs out or closes the terminal.
</details>

---

## Short Answer Questions (3 Questions)

### Question 8
Explain the relationship between `fork()` and `exec()`. How do they work together in process creation?

<details>
<summary>Answer</summary>

**fork()**: Creates a new child process that is an exact duplicate of the parent process. It returns different values:
- Returns 0 to the child process
- Returns the child's PID to the parent

**exec()**: Replaces the current process image with a new program. The process keeps its PID but the code it runs changes.

**Together**:
```bash
# Shell's process creation
fork()  → Child process created (copy of shell)
exec()  → Child runs /bin/ls (replaces shell code)
```

This pattern allows programs to start new processes without losing the original parent process.
</details>

---

### Question 9
Your system has these zombie processes. Explain what happened and how to fix it:

```bash
$ ps aux | grep defunct
root     5555  0.0  0.0     0   0 ?  Z  10:00  0:00 [sleep] <defunct>
root     5556  0.0  0.0     0   0 ?  Z  10:01  0:00 [sleep] <defunct>
root     5557  0.0  0.0     0   0 ?  Z  10:02  0:00 [sleep] <defunct>
```

<details>
<summary>Answer</summary>

**What happened**: 
- These `sleep` processes exited but their parent process (PPID) didn't call `wait()` to harvest their exit status
- The processes are dead but still appear in the process table as zombies
- The parent is likely a script that starts children but doesn't wait for them

**How to fix**:
1. Find the parent process: `ps -o ppid= -p 5555`
2. Kill the parent: `kill -9 [PARENT_PID]`
3. The zombies will be reaped by init (PID 1)

**Prevention** (in scripts):
```bash
#!/bin/bash
sleep 100 &
wait $!  # Wait for child to finish
```

**Note**: Zombies don't use system resources (no memory/CPU) but pollute the process table.
</details>

---

### Question 10
You need to start a long-running process that should:
1. Survive if you log out
2. Run in the background
3. Not stop if the terminal is closed
4. Not interfere with your shell

Show the command and explain each part.

<details>
<summary>Answer</summary>

**Correct command**:
```bash
nohup ./long_running_job.sh > output.log 2>&1 &
```

**Explanation**:
- `nohup`: Makes process immune to SIGHUP (terminal hangup)
- `./long_running_job.sh`: The program to run
- `> output.log`: Redirect stdout to file (default: nohup.out)
- `2>&1`: Redirect stderr to same file
- `&`: Run in background (return shell prompt immediately)

**Result**: Process continues running after logout

**Alternative with disown** (if already running):
```bash
./long_running_job.sh &
disown %1
logout  # Process survives
```

**Verification**:
```bash
nohup sleep 3600 > /tmp/test.log 2>&1 &
logout
# In another terminal:
ps aux | grep sleep  # Still running!
```
</details>

---

## Answer Key Summary

| Question | Answer |
|----------|--------|
| 1 | B |
| 2 | C |
| 3 | C |
| 4 | B |
| 5 | B |
| 6 | C |
| 7 | B |
| 8 | See detailed answer above |
| 9 | See detailed answer above |
| 10 | See detailed answer above |

---

## Scoring Guide

- **9-10 correct**: Expert understanding of process management
- **7-8 correct**: Strong grasp of core concepts
- **5-6 correct**: Adequate knowledge, review signals and zombie handling
- **Below 5**: Study fork/exec and signal handling more carefully

---
