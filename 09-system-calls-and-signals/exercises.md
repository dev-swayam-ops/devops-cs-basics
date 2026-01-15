# 09 - System Calls and Signals Exercises

## Easy Exercises (1-5)

### Exercise 1: List All Signals
**Objective**: Know available signals.

**Task**:
- Run: `kill -l`
- How many signals total?
- Find SIGTERM, SIGKILL, SIGSTOP
- What's number for each?
- Can you kill with number (9) or name?

---

### Exercise 2: Send SIGTERM to Process
**Objective**: Gracefully terminate.

**Task**:
- Start: `sleep 100 &`
- Get PID: `echo $!`
- Send: `kill -TERM [PID]`
- What happens? (should exit)
- Is it immediate?

---

### Exercise 3: Force Kill with SIGKILL
**Objective**: Forcefully stop process.

**Task**:
- Start: `sleep 100 &`
- PID: `jobs -l`
- Kill: `kill -9 [PID]`
- What's difference from SIGTERM?
- Can process ignore SIGKILL?

---

### Exercise 4: Pause and Resume
**Objective**: Control process execution.

**Task**:
- Start: `sleep 100 &`
- Pause: `kill -STOP [PID]`
- Check: `ps aux | grep sleep` (state?)
- Resume: `kill -CONT [PID]`
- Is process back to normal?

---

### Exercise 5: Check Process Group Signals
**Objective**: Signal multiple processes.

**Task**:
- Start 3 sleep: `sleep 100 & sleep 100 & sleep 100 &`
- Send to all: `pkill -TERM sleep`
- All terminated?
- Faster than kill one-by-one?

---

## Medium Exercises (6-10)

### Exercise 6: Trap Signal in Bash
**Objective**: Handle signal in script.

**Task**:
```bash
bash -c 'trap "echo Caught!" TERM; sleep 30'
```
- In another terminal: `pkill -TERM -f "Caught"`
- Does output appear?
- Can you trap multiple signals?

---

### Exercise 7: Ignore Signal with Trap
**Objective**: Block signal handling.

**Task**:
```bash
bash -c 'trap "" TERM; sleep 30' &
```
- Send: `kill -TERM [PID]`
- Does process exit?
- Does trap "" work for all signals?

---

### Exercise 8: Identify Zombie Process
**Objective**: See defunct process.

**Task**:
- Run: `{ sleep 1 & } ; ps aux | grep defunct`
- Any zombies?
- Run: `python3 -c "import os; os.fork(); os.exit()"`
- Check: `ps aux`
- How to clean up?

---

### Exercise 9: Send Signal by Name
**Objective**: Use signal names instead of numbers.

**Task**:
- Start: `sleep 100 &`
- Kill by name: `kill -SIGTERM [PID]`
- Or by number: `kill -15 [PID]`
- Same effect?
- Names easier to remember?

---

### Exercise 10: Handle SIGINT in Script
**Objective**: Catch Ctrl+C.

**Task**:
```bash
bash -c 'trap "echo Interrupted!" INT; sleep 30'
```
- Press: Ctrl+C
- Message appears?
- Process exits or continues?
- INT = SIGINT = signal 2?

---

## Submission Tips

1. Use `kill -l` to see all signal numbers
2. Use `pkill` for multiple processes
3. Remember: -9 is SIGKILL (strongest)
4. Trap early in scripts
5. Always check PID before killing
