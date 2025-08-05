Here is a well-formatted Markdown version of your Operating System notes, covering all units and lectures provided:

# Operating Systems Full Notes

## UNIT 1: An Introduction to Operating Systems

### What is an Operating System?

- **System software** operates and controls the computer, providing a platform for application software.
- An operating system (OS) is software that manages all computer resources (hardware & software).
- It provides an environment for efficient and convenient program execution, hiding hardware complexity and acting as a resource manager.

#### Why OS?

1. **Without OS:**
    - Apps must include bulky hardware interaction code.
    - No resource arbitration (a single app can hog all resources).
    - No memory protection.

2. **What is an OS made up of?**
    - Collection of system software.

#### Primary OS Functions

- Access to computer hardware.
- Interface between user and hardware.
- Resource management (memory, files, devices, security, processes, etc.).
- Hides hardware complexity (abstraction).
- Supports app execution with isolation and protection.

#### OS Goals

- Maximum CPU utilization.
- Minimize process starvation.
- Promote execution of higher priority jobs.

### Types of Operating Systems

- **Single Process OS:** Only 1 process executes at a time.
- **Batch-Processing OS:** Jobs grouped and processed in batches.
- **Multiprogramming OS:** Multiple jobs in memory; increases CPU utilization via context switching.
- **Multitasking OS:** Logical extension of multiprogramming, better responsiveness.
- **Multiprocessing OS:** More than 1 CPU in a computer; increases reliability & throughput.
- **Distributed OS:** Manages resources across multiple interconnected nodes.
- **Real Time OS (RTOS):** Error-free operation within tight-time boundaries (e.g., robots, air traffic control).

## LEC-3: Multi-Tasking vs Multi-Threading

- **Program:** Executable file with instructions.
- **Process:** Program under execution (in RAM).
- **Thread:** Single sequence of execution within a process (light-weight, for parallelism).

#### Multi-Tasking vs Multi-Threading

| Multi-Tasking                            | Multi-Threading                              |
|------------------------------------------|----------------------------------------------|
| Execution of more than one task (process)| Single process divided into several threads  |
| Each process has isolation and memory    | Threads share resources of the process       |
| Context switching between processes      | Context switching between threads            |

#### Thread & Process Context Switching

| Thread Context Switching                | Process Context Switching         |
|-----------------------------------------|-----------------------------------|
| State change within same process        | Switches between different processes|
| Faster, no address space switch         | Slower, includes address space    |
| CPU cache preserved                     | CPU cache flushed                 |

## LEC-4: OS Components

1. **Kernel:** Core component interacting directly with hardware.
2. **User space:** Where applications run; interacts with kernel via GUI/CLI (shell).
3. **Shell:** Command interpreter between user and kernel.

### Kernel Functions

- Process management (scheduling, creation, suspension, synchronization)
- Memory management
- File management (create/delete files/directories, backup)
- I/O management (buffering, caching, spooling)

### Types of Kernels

- **Monolithic:** All OS functions in a single module (Linux, Unix, MS-DOS)
- **Microkernel:** Only essentials in kernel; rest in user space (L4, Symbian, MINIX)
- **Hybrid Kernel:** Combines speed and modularity (MacOS, Windows NT)
- **Nano/Exokernels:** Minimal core

### Communication between user and kernel: **Inter Process Communication (IPC)**

- Done by shared memory or message passing.

## LEC-5: System Calls

- Interface for user-level programs to request kernel services.
- Required for hardware access, inter-process communication.

**Types:**
1. Process Control
2. File Management
3. Device Management
4. Information Maintenance
5. Communication Management

**Examples:**
- **Windows:** `CreateProcess()`, `CreateFile()`, etc.
- **Unix:** `fork()`, `open()`, `read()`, `pipe()`, etc.

**System calls implemented in C.**

### Boot Process (What happens when you turn on your computer?)

1. CPU runs BIOS (or UEFI) from ROM, which initializes hardware (POST).
2. BIOS loads bootloader from storage (MBR or EFI).
3. Bootloader loads OS kernel.
4. Kernel loads user space.

## Lec-7: 32-Bit vs 64-Bit OS

- 32-bit OS can use 4GB memory; 64-bit much higher.
- 32-bit CPU processes 4 bytes/cycle; 64-bit processes 8 bytes/cycle.
- 64-bit CPUs can run both 32-bit and 64-bit OS.
- More addressable memory, better graphics, and performance with 64-bit.

## Lec-8: Storage Devices Basics

- **Registers:** Smallest/fastest; in CPU.
- **Cache:** Temporarily stores frequently used data.
- **Main Memory (RAM):** Volatile, fast.
- **Secondary Memory:** Non-volatile, large, slower (HDD/SSD).

| Attribute      | Register | Cache | Main Memory | Secondary |
|---------------|---------|-------|-------------|-----------|
| Cost          | Highest | High  | High        | Low       |
| Access Speed  | Fastest | Fast  | Faster      | Slow      |
| Size          | Small   | Small | Medium      | Largest   |
| Volatility    | Yes     | Yes   | Yes         | No        |

## Lec-9: Introduction to Process

- **Program:** Compiled code on disk.
- **Process:** Program under execution (in RAM).

### Process Creation Steps

- Load program/data into memory
- Allocate stack & heap
- I/O tasks
- OS transfers control

### Process Control Block (PCB)

- Stores process info (ID, state, registers, etc.)
- Used for context switching.

## Lec-10: Process States and Queues

- **Process States:** New, Ready, Run, Waiting, Terminated.
- **Queues:**
  - **Job Queue:** Processes in new state (on disk).
  - **Ready Queue:** Processes ready to run (in RAM).
  - **Waiting Queue:** Processes waiting for I/O.

- **Schedulers:** Long-term (job), Short-term (CPU/ready), Medium-term (swap).
- **Dispatcher:** Loads selected process to CPU.

## LEC-11: Swapping, Context-Switching, Orphan/Zombie Processes

- **Swapping:** Temporarily moving a process to disk to manage memory.
- **Context Switching:** Save/restore state as CPU switches processes (overhead).
- **Orphan Process:** Parent dies before child; "adopted" by init.
- **Zombie Process:** Process finished but not yet "reaped" by parent.

## LEC-12: Intro to Process Scheduling & FCFS

- **Process Scheduling:** OS switches CPU among processes for multi-programming.
- **CPU Scheduling Goals:** Max CPU utilization, min turnaround/wait/response time, max throughput.
- **Scheduling Types:** 
  - Non-Preemptive (FCFS)
  - Preemptive (Round Robin, Priority, SJF)
- **Convoy Effect:** One long process delays others in FCFS.
- **Throughput:** # processes finished/unit time.

## LEC-13: SJF, Priority, RR Scheduling

- **Shortest Job First (SJF):** Least burst time first; suffers if initial process is long.
- **Priority Scheduling:** SJF is special case (priority = 1/BT); can lead to starvation.
- **Aging:** Increase priority of waiting processes to prevent starvation.
- **Round Robin:** Each process gets a time quantum (TQ). Context switches occur at every TQ. Good for time-sharing.

## LEC-14: MLQ & MLFQ

- **MLQ (Multi-Level Queue):**
  - Ready queue split into several sub-queues (system, interactive, batch processes), each with its own scheduler.
  - Starvation and convoy effect present.
- **MLFQ (Multi-Level Feedback Queue):**
  - Allows process to move between queues based on behavior.
  - Improves flexibility, reduces starvation, includes "aging".
- **Comparison Table:** FCFS, SJF, preemptive/non-preemptive priority, RR, MLQ, MLFQ (see original for details).

## LEC-15: Concurrency Basics

- **Concurrency:** Multiple instruction sequences running at the same time (via threads).
- **Threads:** Individual execution paths within a process.
- **Thread context switching is fast** (no address space switch).
- Multi-threading benefits: Responsiveness, resource sharing, economical, better multi-core use.

## LEC-16: Critical Section Problem

- **Critical Section:** Code part accessing shared resources.
- **Race Condition:** Unpredictable behavior if two threads update shared data at once.
- **Solution:** Use locks (mutual exclusion), atomic operations, semaphores.
- Flags aren't enough; use Peterson’s solution for two threads.
- Locks can cause contention, debugging challenges, and deadlocks.

## LEC-17: Conditional Variables and Semaphores

- **Condition Variable:** Lets threads wait for a condition (avoids busy-waiting).
- **Semaphore:** Integer for controlling access to shared resource.
    - **Binary Semaphore:** Like a mutex (0/1).
    - **Counting Semaphore:** For >1 resource instances.
- Avoids busy-waiting using wait/signal.

## LEC-20: Dining Philosophers Problem

- Classic demonstration of resource sharing & deadlock.
- Solution uses semaphores for forks but must avoid deadlock (e.g., odd/even, limiting philosophers).

## LEC-21: Deadlock Part-1

- **Deadlock:** Processes stuck waiting for resources held by each other.
- **Conditions (must all hold):** Mutual exclusion, Hold & wait, No preemption, Circular wait.
- **Handling Strategies:**
    - Prevention (deny one condition)
    - Avoidance (Banker’s algorithm)
    - Ignore (Ostrich algorithm)

## LEC-22: Deadlock Part-2

- **Avoidance:** System checks if current request leads to safe state.
- **Safe State:** System can allocate resources in some order to avoid deadlock.
- **Algorithms:** Banker algorithm, Wait-for graph.
- **Recovery:**
    - Abort processes
    - Preempt resources

## LEC-24: Memory Management Techniques

- **Memory must be shared** among processes in multi-programming.
- **Address Spaces:**
    - Logical (virtual): Address generated by CPU
    - Physical: Actual location in RAM, mapped by MMU

### Contiguous Memory Allocation

- Each process gets a single contiguous memory block.
- **Partitioning:**
    - **Fixed:** Divided into static partitions (leads to internal fragmentation)
    - **Dynamic:** Partition created as needed (no internal fragmentation)
- **Fragmentation:**
    - **Internal:** Wasted space inside partitions.
    - **External:** Total unused space not contiguous.

## LEC-25: Free Space Management

- **Compaction** (defragmentation): Move processes so free space is together.
- **Free list:** Linked list of free memory holes.
- **Allocation algorithms:** 
    - First Fit, Next Fit, Best Fit, Worst Fit

## LEC-26: Paging

- **Paging:** Flexible, non-contiguous allocation to remove external fragmentation.
    - **Physical memory:** Fixed-size blocks (frames).
    - **Logical memory:** Fixed-size pages.
    - **Page Table:** Maps pages to frames.
    - **TLB:** Hardware cache for address translation.

## LEC-27: Segmentation

- User view of memory: Memory divided into logical segments (variable size).
- **Pros:** No internal fragmentation; segments are contiguous and logical.
- **Cons:** External fragmentation.

**Modern systems combine paging and segmentation.**

## LEC-28: Virtual Memory

- Allows process execution not fully loaded in RAM (illusion of big memory).
- **Demand Paging:** Pages loaded as needed (on page fault).
- **Pager:** Loads/modifies memory pages.
- **Benefits:** Run large apps, more programs in memory.
- **Downside:** Thrashing possible (excessive paging).

## LEC-29: Page Replacement Algorithms

- **When page fault occurs:** OS must bring needed page into memory.
- **Algorithms:**
    - FIFO (can suffer Belady’s anomaly)
    - Optimal (lowest faults, but impractical)
    - LRU (least recently used, via counters or stack)
    - LFU/MFU (less common, counting-based)

## LEC-30: Thrashing

- Too few frames allocated → frequent page faults, very slow performance.
- **Handling:**
    - **Working set model:** Allocate enough frames for current locality.
    - **Page fault frequency:** Adjust allocation based on current fault rate.

**End of Notes**
