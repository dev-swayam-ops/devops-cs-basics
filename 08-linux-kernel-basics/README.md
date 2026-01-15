# 08 - Linux Kernel Basics

## What You'll Learn

- Kernel architecture and purpose
- System calls (syscalls)
- Kernel vs user space
- Process states and context switching
- Interrupt handling
- Kernel modules
- dmesg logs and kernel messages
- Kernel parameters and tuning

## Prerequisites

- Completed **07-dns-and-http-basics**
- Understanding of processes and OS basics
- Familiarity with Linux command line

## Key Concepts

### 1. Kernel Architecture

The kernel is the core of the operating system that manages all hardware and system resources. It operates in a privileged mode separate from user applications.

**User Space**
- Where regular applications run (web browsers, editors, servers)
- Cannot directly access hardware or system resources
- Restricted environment - if one app crashes, doesn't crash system
- Has access to system through system calls only
- Each process gets isolated address space
- Libraries (libc) in user space provide interface to system calls
- Shells (bash, sh) are user space programs that invoke other programs

**Kernel Space**
- Runs in privileged "kernel mode" with full hardware access
- Only kernel code runs here, not user applications
- Has direct access to all hardware, memory, I/O
- One kernel per system (shared by all processes)
- Completely isolated from user space
- Bug in kernel can crash entire system

**System Call Interface**
- The boundary between user and kernel space
- Controlled gateway - only way user applications can request kernel services
- When user application calls `open()`, library looks up system call and triggers transition
- Mode switch from user to kernel mode is expensive (100+ CPU cycles)
- Kernel performs requested operation then switches back to user mode

**Major Kernel Subsystems**

**Process Management**
- Tracks all running processes
- Scheduling: decides which process gets CPU time
- Signals: delivers asynchronous notifications
- Synchronization: coordinates access to shared resources

**Memory Management**
- Virtual memory system
- Paging: swapping pages between RAM and disk
- Allocation: malloc/free eventually request from kernel
- Protection: prevents processes from accessing each other's memory

**File Systems**
- Manages storage organization
- File operations: open, read, write, close
- Directory hierarchy
- Permissions and access control
- Buffer cache for I/O optimization

**Device Drivers**
- Interface between hardware and software
- Talks to hardware directly
- Handles interrupts from devices
- Loaded as modules or compiled in

**Networking**
- Network protocol implementation (TCP/IP, UDP)
- Socket API for applications
- Packet routing and forwarding
- Firewall/iptables

**Hardware Interface**
- Lowest level kernel code
- CPU-specific assembly
- Interrupt handling
- Context switching

### 2. System Calls (Syscalls)

System calls are the mechanism through which user programs request kernel services. Every interaction with hardware goes through a system call.

**What Are System Calls**
- Interface that user programs use to request kernel services
- Transition from user mode to kernel mode
- Kernel performs privileged operation then returns result to user
- Not normal function calls - involves mode switch and overhead

**How System Calls Work**
1. User application calls library function (e.g., `open()`)
2. Library function loads syscall number into CPU register
3. Library triggers "syscall" instruction
4. CPU switches to kernel mode
5. Kernel looks up syscall handler based on number
6. Kernel executes the syscall handler
7. Kernel stores result in register
8. Kernel triggers "return from syscall" instruction
9. CPU switches back to user mode
10. Library returns result to application

**Common System Calls**
- **Process**: `fork()` (create), `exec()` (replace), `exit()` (terminate), `wait()` (wait for child)
- **File**: `open()`, `read()`, `write()`, `close()`, `stat()` (get info)
- **Memory**: `brk()`, `mmap()` (allocate), `munmap()` (free)
- **Signal**: `signal()`, `sigaction()` (set handler), `kill()` (send signal)
- **IPC**: `pipe()`, `socket()`, `semget()` (communication)

**Why System Calls Are Expensive**
- Mode switch from user to kernel: ~100 CPU cycles
- Context switch: save/restore CPU state
- TLB flush: page table translations become invalid
- Cache misses: working set changes
- Interrupt disabled: prevents responsive to external events
- Every system call has minimum 100-500 cycle overhead
- Why: reduce system calls in performance-critical code

**Syscalls Go Through libc**
- User programs don't directly make syscalls (usually)
- libc library provides C functions that are user-friendly wrappers
- `read()` in libc translates to syscall and handles errors
- errno variable holds error code if syscall fails
- Some libraries add buffering on top (e.g., stdio buffering around read/write syscalls)

**Virtual vs Real Syscalls**
- Some calls like `getpid()` are handled by kernel without switching modes (vdso - virtual syscall)
- Faster than real syscalls but limited functionality
- Kernel provides pointer to data that doesn't change - process just reads it

### 3. Process States and Context Switching

Processes constantly transition between different states as they execute. The scheduler manages these transitions to fairly allocate CPU time.

**New (Created)**
- Process just created with `fork()` or `execve()`
- Resources allocated
- Quickly transitions to Ready

**Ready (Runnable)**
- Process is ready to run, waiting for CPU
- In ready queue maintained by scheduler
- Waiting for scheduler to select it
- Could be ready immediately but CPU busy with another process
- Thousands of processes can be ready simultaneously

**Running (Executing)**
- Process currently executing on CPU
- Only one process per CPU core can be running at a time
- Runs for time slice (quantum) - typically 10-100ms
- After time slice expires, scheduler preempts it (forces switch)
- Can voluntarily yield (e.g., sleep) or block on I/O

**Blocked (Waiting)**
- Process waiting for event (I/O completion, signal, lock)
- Not in ready queue - scheduler ignores it
- Doesn't consume CPU time while blocked
- When event occurs (file data arrives, signal delivered), moved to Ready
- Most processes spend most time blocked (waiting for I/O)

**Zombie (Reaped)**
- Process has exited but parent hasn't called `wait()` to read exit code
- No resources consumed (memory freed)
- Small entry remains in process table holding exit code
- If parent dies before reading exit code, init adopts zombie and reaps it
- Indicates parent process bug

**Terminated (Exited)**
- Process completely removed from system
- All resources freed
- PID can be reused
- No trace remains in process table

**State Transitions**
- New → Ready (immediately)
- Ready → Running (scheduler selects)
- Running → Ready (preempted by scheduler after time slice)
- Running → Blocked (process waits for I/O or event)
- Blocked → Ready (event occurs, returns to queue)
- Running → Zombie (calls `exit()`, parent hasn't waited)
- Zombie → Terminated (parent calls `wait()`)

**Context Switch**
- When scheduler switches from one process to another
- Save all CPU registers and state of current process
- Restore all CPU registers and state of new process
- TLB (translation lookaside buffer) flushed
- CPU caches become invalid (new process likely to miss cache)
- 1000-10000 CPU cycles overhead
- Too many context switches = thrashing (system spends time switching, not working)

### 4. Interrupts

Interrupts are asynchronous events that require immediate kernel attention. They're essential for I/O and multitasking.

**Hardware Interrupts**
- External events: keyboard key pressed, disk data ready, network packet arrived
- Triggered by hardware, not by any software
- CPU pauses current process, jumps to interrupt handler
- Handler is kernel code specific to interrupt
- After handler finishes, CPU resumes process where it left off
- Examples: timer interrupt (scheduling), keyboard interrupt, disk interrupt

**Software Interrupts**
- Triggered by software: system calls, divide by zero, invalid memory access
- Same mechanism as hardware interrupts
- Examples: `int 0x80` (legacy syscall instruction), CPU exceptions
- Synchronous - directly caused by executing code

**Exception Handling**
- Segmentation fault: tried to access invalid memory
- Division by zero: attempted `x / 0`
- Illegal instruction: CPU doesn't recognize instruction
- Page fault: virtual address not mapped to physical memory
- Kernel exception handler terminates process (usually)

**Interrupt Handler (ISR - Interrupt Service Routine)**
- Kernel code that runs when interrupt occurs
- Must be very fast - system unresponsive while running
- Often just acknowledges interrupt and schedules deferred work
- Cannot do blocking operations or lengthy computations
- Runs in special interrupt context (not inside any process)

**Context Switch During Interrupt**
- Not technically a context switch (not switching between processes)
- Interrupting process state saved
- Interrupt handler runs
- Interrupting process state restored
- If scheduler decides to switch processes, happens after handler returns

**Interrupt Priority**
- Some interrupts more important than others
- Timer interrupt (scheduling) very high priority
- Device interrupts vary
- Interrupts can be masked (disabled) temporarily
- Nested interrupts: interrupt handler can be interrupted by higher priority

**Overhead of Interrupts**
- Each interrupt causes context save/restore
- Frequency: hundreds or thousands per second on busy system
- Too many interrupts = system busy handling them, no time for application work
- Interrupt storms: broken device generates thousands of interrupts/second (hangs system)

### 5. Kernel Modules

Kernel modules allow extending kernel functionality without recompiling the entire kernel. They provide a way to add device drivers and features dynamically.

**What Are Kernel Modules**
- Kernel extensions loaded at runtime
- Object files linked into running kernel
- Can be loaded and unloaded on the fly
- Run in kernel space with full hardware access
- Examples: device drivers, filesystem implementations, protocol handlers

**Advantages**
- Faster kernel development: modify module, reload, test (no reboot)
- Flexible: load only drivers needed for hardware
- Smaller kernel in memory: optional features loaded as needed
- Bug isolation: buggy module can often be unloaded without reboot

**Disadvantages**
- Buggy module can crash kernel
- Security: malicious module has full system access
- Complexity: module must follow kernel interfaces correctly
- Dependencies: modules can depend on other modules

**Module Naming Convention**
- Stored in `/lib/modules/$(uname -r)/kernel/`
- Organized by category: drivers/usb/, drivers/net/, fs/ (filesystems), etc.
- File extension: `.ko` (kernel object)
- Named after functionality: `e1000.ko` for Intel network driver

**Loading and Unloading**
- `modprobe driver_name` - loads module and dependencies
- `insmod path/to/module.ko` - low-level load (must handle dependencies manually)
- `rmmod driver_name` - unloads module (fails if in use)
- `lsmod` - lists currently loaded modules
- `modinfo driver_name` - shows module information

**Module Parameters**
- Modules can accept parameters at load time
- Example: `modprobe e1000 debug=1` enables debugging
- Parameters shown in sysfs: `/sys/module/driver_name/parameters/`

**Common Modules**
- Network drivers: `e1000`, `ixgbe`, `r8169`
- Storage drivers: `ahci`, `usb_storage`
- Filesystem drivers: `ext4`, `xfs`, `nfs`
- System modules: `apparmor`, `xt_conntrack` (netfilter)

**Device Files**
- Modules create device files: `/dev/something`
- Example: `e1000` driver creates network interface `eth0`
- Applications use device files to interact with hardware
- Device files managed by udev (dynamic device manager)

## Hands-on Lab: Kernel Exploration

### Lab Overview
Examine kernel messages, modules, and parameters.

### Lab Commands

```bash
# 1. Show kernel version
uname -a

# Expected:
# Linux myhost 5.10.0 #1 SMP x86_64 GNU/Linux

# 2. Kernel release
uname -r

# Expected: 5.10.0 or similar

# 3. List kernel modules
lsmod | head

# Expected:
# Module  Size  Used by
# overlay 65536 2
# ext4    614400 3

# 4. Load module (if you have permission)
sudo modprobe module_name

# Expected: (no output means success)

# 5. Unload module
sudo modprobe -r module_name

# Expected: (or error if in use)

# 6. Kernel messages
dmesg | head -20

# Expected:
# [0.000000] Linux version 5.10...
# [0.000000] Command line: BOOT_IMAGE=...

# 7. Recent kernel errors
dmesg | grep -i error

# Expected: (any errors, if any)

# 8. Kernel parameters
sysctl -a | head

# Expected:
# kernel.pid_max = 4194304
# kernel.shmmax = 68719476736

# 9. Change kernel parameter (temporarily)
sudo sysctl -w kernel.shmmax=1073741824

# Expected: kernel.shmmax = 1073741824

# 10. Check /proc/sys directly
cat /proc/sys/kernel/pid_max

# Expected: 4194304 (max PID)
```

## Validation

```bash
# Can you check kernel version?
uname -a && echo "✓ Kernel version shown"

# See kernel modules?
lsmod && echo "✓ Modules listed"

# View kernel messages?
dmesg | head && echo "✓ Messages visible"

# Access kernel parameters?
sysctl kernel.pid_max && echo "✓ Parameters shown"
```

## Cleanup

No cleanup needed (read-only except for sysctl changes).

## Common Mistakes

1. **User vs kernel**: User space can't directly access hardware
2. **Syscall overhead**: Each syscall switches mode (expensive)
3. **Context switch**: Process scheduling has overhead
4. **Modules permanent**: Changes lost on reboot (use /etc/sysctl.conf)
5. **Interrupt handlers**: Must be fast (don't do I/O)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Module won't load | Check: lsmod, dmesg for errors |
| Parameter won't change | Check: permissions, sysctl.conf |
| Kernel panic | Check: dmesg, hardware issues |
| High interrupts | Check: irq usage, interrupt storm |
| Memory issues | Check: kernel.shmmax, limits |

## Next Steps

1. Complete 10 exercises in `exercises.md`
2. Review solutions in `solutions.md`
3. Use `cheatsheet.md` for commands
4. Move to **09-system-calls-and-signals** after completion

## Additional Resources

- Kernel: `man 7 kernel`, `man uname`
- Syscalls: `man 2 syscalls`
- Modules: `man modprobe`, `man lsmod`
- Parameters: `man sysctl`, `man 5 sysctl.conf`

