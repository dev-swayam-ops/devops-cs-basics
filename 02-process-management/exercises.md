# 02 - Process Management Exercises

## Easy Exercises (1-5)

### Exercise 1: Start and Monitor Background Process
**Objective**: Launch a process in background and monitor it.

**Task**:
- Start a long-running process in background (e.g., `sleep 3600 &`)
- List background jobs using `jobs`
- Note the job number and PID
- What state is the process in?

---

### Exercise 2: Send Signals to Process
**Objective**: Communicate with process using signals.

**Task**:
- Start a background sleep process
- Send SIGTERM signal using `kill -TERM [PID]`
- What happened to the process?
- How is this different from SIGKILL?

---

### Exercise 3: Find Processes by Name
**Objective**: Locate specific processes.

**Task**:
- Start multiple background sleep processes
- Use `pgrep sleep` to find all sleep processes
- Use `pidof sleep` to get PIDs
- Kill all sleep processes using `killall sleep`

---

### Exercise 4: Check Process Parent-Child Relationship
**Objective**: Understand process hierarchy.

**Task**:
- Get your current shell PID using `echo $$`
- Use `ps -o pid,ppid,cmd` to show hierarchy
- Who is your shell's parent?
- Start a background process - what is its parent?

---

### Exercise 5: View Process Details
**Objective**: Extract detailed process information.

**Task**:
- Pick a running process (e.g., bash, init)
- Use `ps -fp [PID]` to show full details
- What do VSZ and RSS mean?
- What is STAT column showing?

---

## Medium Exercises (6-10)

### Exercise 6: Create Parent-Child Process Chain
**Objective**: Understand process creation and relationships.

**Task**:
- Open one shell (Shell 1)
- In Shell 1, start a background bash: `bash &`
- In that bash, start another: `bash &`
- Use `pstree` to show the hierarchy
- How many shells deep can you nest?

---

### Exercise 7: Monitor Process State Changes
**Objective**: Watch process state transitions.

**Task**:
- Start a process and immediately suspend it (Ctrl+Z)
- Check its state: `ps -o pid,stat,cmd` (state should be T for stopped)
- Resume it: `bg`
- State should change back to S or R
- Modify state by sending SIGSTOP and SIGCONT

---

### Exercise 8: Handle Zombie Process
**Objective**: Understand zombie process creation.

**Task**:
- Create a zombie: `bash -c 'sleep 1 &' &` (exits parent, child becomes zombie)
- Immediately run `ps -o pid,ppid,stat,cmd | grep sleep`
- Look for `Z` state in STAT column
- What is the PPID? (should be 1 = init)
- Zombie will disappear after child terminates

---

### Exercise 9: Control Foreground and Background
**Objective**: Master job control.

**Task**:
- Start a process: `sleep 100`
- Suspend it: Ctrl+Z
- List jobs: `jobs -l`
- Resume in background: `bg`
- Bring to foreground: `fg %1` (replace 1 with job number)
- How many times can you switch between fg/bg?

---

### Exercise 10: Signal Handler Demonstration
**Objective**: See which signals work on which processes.

**Task**:
- Start: `sleep 3600 &`
- Try SIGTERM: `kill -TERM [PID]` → process should terminate
- Start another: `sleep 3600 &`
- Try SIGSTOP: `kill -STOP [PID]` → process should suspend
- Resume: `kill -CONT [PID]`
- Verify with `ps -o pid,stat,cmd`

---

## Submission Tips

1. Always record PID and job number from output
2. Compare signals: SIGTERM (graceful) vs SIGKILL (forced)
3. Understand state letters: R=running, S=sleeping, T=stopped, Z=zombie
4. Try variations: `kill vs killall vs pkill`
5. Explore job control in different shells (bash, zsh, etc.)
