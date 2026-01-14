# 02 - Process Management: Exercises

## Exercise 1: Understanding Process IDs (Easy)

Run these commands and answer the questions:

```bash
echo $$
ps -p $$
echo $PPID
```

1. What is your current shell's PID?
2. What is your shell's parent PID?
3. What process is your shell's parent?

---

## Exercise 2: Process Hierarchy (Easy)

Run:

```bash
pstree -p
```

1. Find `systemd` (PID 1) in the output
2. Identify at least 3 child processes of `systemd`
3. Find your shell process and its parent

---

## Exercise 3: Finding Processes by Name (Easy)

Use `ps` and `grep` commands to:

```bash
ps aux | grep bash
ps aux | grep sleep
pgrep bash
```

1. Count how many bash processes are running
2. Get the PID of your current bash shell
3. Compare the output of `ps | grep` vs `pgrep`

---

## Exercise 4: Process States (Easy)

Create processes in different states:

```bash
sleep 100 &
sleep 200 &
sleep 300 &
sleep 400 &
jobs -l
ps aux | grep sleep
```

1. List all sleep processes using `jobs`
2. List them using `ps aux`
3. Identify the state of each process
4. What is the difference between `jobs` and `ps aux` output?

---

## Exercise 5: Signals and Process Termination (Easy)

```bash
sleep 60 &
PID=$!
kill -TERM $PID  # or kill -15
kill -l          # List all signals
```

1. What is the difference between SIGTERM and SIGKILL?
2. When should you use each?
3. What signal is sent by default with `kill` command?

---

## Exercise 6: Background and Foreground Jobs (Medium)

Run:

```bash
sleep 100
# Press Ctrl+Z to suspend
bg              # Resume in background
fg              # Bring to foreground
# Press Ctrl+Z again
kill %1         # Kill by job number
```

1. What does Ctrl+Z do?
2. What's the difference between `bg` and `fg`?
3. How do you kill a job by number instead of PID?
4. Start 3 background jobs and bring the 2nd one to foreground

---

## Exercise 7: Process Resource Usage (Medium)

Monitor process resource usage:

```bash
# Start a memory-heavy process
python3 -c "import time; x = [0]*10000000; time.sleep(100)" &

# Monitor it
ps aux --sort=-%mem | head -5
top -n 1 -p [PID]
```

1. Which process is using the most memory?
2. How much CPU is it using?
3. How much virtual memory (VSZ) is it using?
4. What is the difference between VSZ and RSS?

---

## Exercise 8: Zombie Processes (Medium)

Create a zombie process scenario:

```bash
# Start a parent process that doesn't wait for children
bash -c "bash -c 'echo test; exit 0' &" &
sleep 1
ps aux | grep defunct
```

1. What is a zombie process?
2. Why do zombie processes exist?
3. How can you prevent them?
4. How do you clean up zombie processes?

---

## Exercise 9: Process Communication via Signals (Medium)

Write a simple script and send it signals:

```bash
# Create a script
cat > test_signal.sh << 'EOF'
#!/bin/bash
trap 'echo "Received SIGTERM"' TERM
trap 'echo "Received SIGINT"' INT
echo "PID: $$"
while true; do
    echo "Running..."
    sleep 1
done
EOF

chmod +x test_signal.sh
./test_signal.sh &
PID=$!

kill -TERM $PID
sleep 1
kill -INT $PID
```

1. What does `trap` do?
2. How can processes respond to signals?
3. Can you trap SIGKILL? Why or why not?
4. Modify the script to handle SIGUSR1

---

## Exercise 10: Process Accounting and Audit (Medium)

Track process execution:

```bash
# Enable process accounting (requires root)
sudo psacct          # or acct package

# Check last processes executed
last

# Use 'w' to see who's logged in
w

# Get detailed process info
cat /proc/[PID]/status
cat /proc/[PID]/cmdline
```

1. What information is available in `/proc/[PID]/` for each process?
2. How can a DevOps engineer use this for security auditing?
3. Create a monitoring script that logs all new process creation
4. How would you detect unauthorized process execution?

---
