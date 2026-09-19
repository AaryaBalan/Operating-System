# Process Management in Operating Systems

> **Placement-focused notes:** First understand the concept with simple examples, then learn the technical definition, states, important terms, and interview questions.

---

## 1. What is Process Management?

A **process** is a **program that is currently running**.

For example:

```text
Program:
Chrome.exe

When stored on disk:
        ↓
      Program

When you open Chrome:
        ↓
      Process
```

A program is like a **recipe**.

A process is like **actually cooking that recipe**.

The Operating System (OS) is responsible for managing these running programs.

### Simple Example

Suppose you are using your computer:

```text
Chrome
VS Code
Spotify
File Explorer
Terminal
```

All of them may need the CPU.

But normally, a CPU core can execute only a limited number of instructions at a time.

So the OS has to decide:

> "Which process should get the CPU now?"

This is one of the main jobs of **Process Management**.

---

# 2. What Does Process Management Mean?

**Process Management** is the part of the Operating System responsible for:

* Creating processes
* Scheduling processes
* Giving CPU time to processes
* Suspending and resuming processes
* Terminating processes
* Managing communication between processes
* Synchronizing processes
* Handling problems such as deadlocks
* Performing context switching

In simple terms:

```text
                 Operating System
                        |
                 Process Management
                        |
       ┌────────────────┼────────────────┐
       ↓                ↓                ↓
   Create            Schedule         Terminate
   Process            Process          Process
       |
       ↓
 Synchronize
       |
       ↓
 Communicate
```

---

# 3. Why Do We Need Process Management?

Imagine you are running:

```text
1. Chrome
2. VS Code
3. Spotify
4. Calculator
```

All four want CPU time.

If the OS did not manage them properly:

* One program could occupy the CPU for too long.
* Other programs could become unresponsive.
* Two processes could modify the same resource incorrectly.
* Processes could wait forever for each other.
* CPU resources could be wasted.

Therefore, the OS acts like a **manager** that coordinates processes.

---

# 4. Program vs Process

This is a very common placement interview question.

| Program                    | Process                                           |
| -------------------------- | ------------------------------------------------- |
| Passive                    | Active                                            |
| Stored on disk             | Exists in memory while executing                  |
| Contains instructions      | Contains instructions + execution state/resources |
| Does not execute by itself | Currently executing or waiting to execute         |
| Example: `calculator.exe`  | Running Calculator                                |

### Simple Example

Suppose you have:

```text
calculator.exe
```

This is a **program**.

When you double-click it:

```text
calculator.exe
       ↓
Operating System loads it
       ↓
Process is created
       ↓
CPU executes it
```

So:

> **Program + execution = Process**

This is a simplified explanation; technically, a process also includes resources and execution context.

---

# 5. What Information Does the OS Need About a Process?

The OS needs to remember important information about every process.

For example:

```text
Process ID: 1250
State: Running
Program Counter: 0x1045
CPU Registers: ...
Memory information: ...
Priority: High
Open files: ...
```

This information is stored in a structure called the:

# Process Control Block (PCB)

A **PCB** is a data structure maintained by the Operating System for each process.

You can think of it as the process's **record card**.

---

## 6. Process Control Block (PCB)

A PCB generally contains information such as:

```text
+--------------------------------+
|       Process Control Block    |
+--------------------------------+
| Process ID (PID)               |
| Process State                  |
| Program Counter                |
| CPU Registers                  |
| CPU Scheduling Information     |
| Memory Management Information  |
| Accounting Information         |
| I/O Status Information         |
+--------------------------------+
```

### Important PCB Components

### 1. Process ID

Every process gets an identifier.

```text
Chrome → PID 1200
VS Code → PID 1350
Spotify → PID 1400
```

The PID helps the OS identify a process.

---

### 2. Process State

The OS needs to know whether the process is:

```text
Ready
Running
Waiting
etc.
```

---

### 3. Program Counter

The **Program Counter (PC)** stores the address of the **next instruction** that should be executed.

Example:

```text
Instruction 100
Instruction 101
Instruction 102 ← next
Instruction 103
```

PC:

```text
102
```

---

### 4. CPU Registers

Registers contain temporary information required by the CPU.

For example:

```text
General-purpose registers
Stack Pointer
Program Counter
Status/flags register
```

These are important during **context switching**.

---

### 5. Scheduling Information

The OS may store information such as:

```text
Priority
Scheduling queue information
Time-related scheduling information
```

---

### 6. Memory Information

The OS needs information about the process's memory.

For example:

```text
Code
Data
Heap
Stack
Page tables / memory mappings
```

---

### 7. I/O Information

Information about resources being used by the process.

For example:

```text
Open files
I/O devices
Sockets
```

---

# 7. Process States

A process does not continuously execute on the CPU.

It moves through different states.

The basic process states are:

```text
             +--------+
             |  New   |
             +---+----+
                 |
                 ↓
             +-------+
             | Ready |
             +---+---+
                 |
              Dispatch
                 |
                 ↓
            +---------+
            | Running |
            +----+----+
              /     \
             /       \
        I/O request   \
           ↓           \ Exit
       +---------+      ↓
       | Waiting |   +-----------+
       +----+----+   |Terminated |
            |        +-----------+
        I/O complete
            |
            ↓
         +-------+
         | Ready |
         +-------+
```

Let's understand each state.

---

# 8. New State

The process is being created.

Example:

You open VS Code:

```text
Click VS Code
      ↓
OS creates process
      ↓
Process = NEW
```

The OS prepares the necessary structures and resources.

---

# 9. Ready State

The process is ready to run but is **waiting for the CPU**.

Example:

```text
Chrome → Ready
VS Code → Running
Spotify → Ready
```

Chrome can run, but the CPU is currently being used by another process.

So Chrome waits in the **Ready Queue**.

---

# 10. Running State

The process is currently executing instructions on a CPU.

Example:

```text
CPU
 ↓
Running VS Code
```

Only the process that currently has a CPU core is in the running state on that core.

---

# 11. Waiting / Blocked State

The process cannot continue until some event happens.

Usually, it is waiting for something such as:

* Disk I/O
* Keyboard input
* Network data
* File operation
* Another process
* Some synchronization event

### Example

Suppose a program asks the disk:

> "Give me this file."

The CPU does not need to sit there doing nothing while the disk works.

The process can move:

```text
Running
   ↓
Request disk I/O
   ↓
Waiting
```

The OS can then give the CPU to another ready process.

When the I/O finishes:

```text
Waiting
   ↓
I/O completed
   ↓
Ready
```

---

# 12. Terminated State

The process has finished execution.

Example:

```text
Calculator
    ↓
Calculation completed
    ↓
Process exits
    ↓
Terminated
```

The OS then performs cleanup and releases resources associated with the process.

---

# 13. CPU-Bound vs I/O-Bound Processes

This is **very important for process scheduling interviews**.

Processes behave differently depending on what they spend most of their time doing.

There are two common categories:

```text
CPU-Bound
I/O-Bound
```

---

# 14. CPU-Bound Process

A **CPU-bound process** spends most of its time performing computations.

Example:

```text
Video encoding
Scientific calculations
Large mathematical computation
Compression
Machine-learning computation
```

Imagine:

```text
CPU → CPU → CPU → CPU → CPU
```

The process needs a lot of CPU time.

### Example

Suppose a program calculates:

```text
1 + 2 + 3 + ... + 1,000,000,000
```

It may spend a lot of time using the CPU.

Therefore, it is relatively **CPU-bound**.

---

# 15. I/O-Bound Process

An **I/O-bound process** spends relatively more time waiting for input/output operations.

Examples:

```text
Reading a file
Writing to disk
Waiting for network data
Waiting for keyboard input
Database/network operations
```

Imagine:

```text
CPU → I/O → Waiting → I/O → Waiting
```

The process may use the CPU briefly and then wait for I/O.

---

# 16. CPU-Bound vs I/O-Bound

| Feature       | CPU-Bound       | I/O-Bound            |
| ------------- | --------------- | -------------------- |
| Main activity | CPU computation | I/O operations       |
| CPU usage     | Usually high    | Usually lower/bursty |
| Waiting       | Relatively less | Relatively more      |
| Example       | Video encoding  | Reading from disk    |
| CPU burst     | Longer          | Shorter              |
| I/O wait      | Less frequent   | More frequent        |

> These are relative characteristics, not absolute categories. A real application can contain both CPU-intensive and I/O-intensive phases.

---

# 17. Why Is I/O Important for CPU Scheduling?

Consider this situation:

```text
Process A → Running
```

Process A asks the disk for data.

The disk is much slower than the CPU.

If the OS simply waited:

```text
CPU
 ↓
Wait for disk
 ↓
CPU does nothing
```

CPU utilization would decrease.

Instead:

```text
Process A
   ↓
Requests I/O
   ↓
Waiting
   ↓
CPU is assigned to Process B
   ↓
Process B runs
```

This is one of the main ideas behind **multiprogramming**.

---

# 18. Multiprogramming

**Multiprogramming** means keeping multiple processes in memory so the CPU can switch to another process when the current process is waiting.

Example:

```text
Process A → I/O waiting
                 ↓
              CPU runs
                 ↓
             Process B
```

The goal is to keep the CPU busy as much as possible.

---

# 19. Multitasking

**Multitasking** allows multiple tasks/processes to make progress by sharing CPU time.

For example:

```text
Chrome
VS Code
Spotify
Terminal
```

The OS rapidly schedules CPU time among processes.

From the user's perspective, they appear to run simultaneously.

On a **single CPU core**, they are not literally executing instructions at the exact same instant. The CPU switches between them rapidly.

On a **multi-core CPU**, multiple processes/threads can actually execute simultaneously on different cores.

---

# 20. Process Scheduling

Suppose there are 5 processes:

```text
P1
P2
P3
P4
P5
```

But only one process can use a particular CPU core at a time.

The OS needs to decide:

> Which process should run next?

This is called **CPU scheduling**.

The component responsible for making scheduling decisions is generally called the **scheduler**.

---

# 21. Why Do We Need CPU Scheduling?

Good scheduling tries to achieve goals such as:

* High CPU utilization
* High throughput
* Low waiting time
* Low turnaround time
* Low response time
* Fairness

These goals can sometimes conflict with each other.

---

# 22. Important Scheduling Terms

### CPU Utilization

Percentage of time the CPU is busy.

```text
CPU busy = 90%
CPU idle = 10%
```

Higher utilization is generally desirable, but maximizing it alone is not enough.

---

### Throughput

Number of processes completed per unit of time.

Example:

```text
100 processes completed
in 10 seconds

Throughput = 10 processes/second
```

---

### Turnaround Time

Total time from process submission to completion.

```text
Turnaround Time
= Completion Time - Arrival Time
```

---

### Waiting Time

Time a process spends waiting in the ready queue.

```text
Waiting Time
= Time spent waiting for CPU
```

---

### Response Time

Time from submitting a request until the system first starts responding to it.

This is especially important for interactive systems.

---

# 23. Process Creation

When a new process is created, the OS needs to set up the process.

Simplified sequence:

```text
Request to create process
          ↓
Create PID
          ↓
Create PCB
          ↓
Set up memory/resources
          ↓
Load program information
          ↓
Put process in appropriate queue
          ↓
Process becomes ready
```

### Example

You open Calculator:

```text
You
 ↓
Open Calculator
 ↓
OS creates process
 ↓
PID assigned
 ↓
PCB created
 ↓
Memory/resources prepared
 ↓
Process enters Ready state
```

---

# 24. Parent and Child Processes

A process can create another process.

The process that creates another process is commonly called the **parent process**.

The newly created process is called the **child process**.

Conceptually:

```text
Parent Process
      |
      +------ Child Process 1
      |
      +------ Child Process 2
```

Operating systems differ in exactly how process creation works.

For example, Unix-like systems commonly use mechanisms such as `fork()` and `exec()`.

---

# 25. Process Termination

A process eventually finishes or is terminated.

For example:

```text
Program completes
       ↓
exit()
       ↓
OS handles termination
       ↓
Resources are released
       ↓
Process information is cleaned up
```

Termination can occur because:

* The process completed normally.
* The process encountered an error.
* The process requested termination.
* Another authorized entity terminated it.
* The OS terminated it under certain conditions.

---

# 26. What Happens When a Process Terminates?

The OS may need to:

```text
Release memory
Close/release resources
Close files
Release other OS-managed resources
Update process information
Clean up the PCB
Notify relevant processes
```

The exact cleanup behavior depends on the operating system.

---

# 27. Context Switching

This is one of the **most important placement concepts**.

Imagine:

```text
CPU is running Process A
```

Now the OS wants to run Process B.

The CPU cannot simply forget Process A's current state.

The OS needs to:

```text
Save A's state
      ↓
Load B's saved state
      ↓
CPU continues B
```

This is called a:

# Context Switch

---

# 28. Simple Context-Switch Example

Suppose you are writing code in VS Code while Spotify is running.

Conceptually:

```text
CPU
 ↓
Process A: VS Code
```

The OS switches:

```text
Save VS Code state
        ↓
Load Spotify state
        ↓
CPU
 ↓
Process B: Spotify
```

Later:

```text
Save Spotify state
        ↓
Load VS Code state
        ↓
CPU
 ↓
Process A: VS Code
```

This happens very quickly.

---

# 29. What Is Saved During a Context Switch?

The exact details depend on the architecture and OS, but the process's execution context can include information such as:

```text
Program Counter
CPU Registers
Stack Pointer
Processor status/flags
Other architecture-specific state
```

This information is associated with the process, commonly through its PCB and related kernel structures.

---

# 30. Why Is Context Switching Necessary?

Without context switching, multitasking would be extremely difficult.

Context switching allows:

```text
Process A
   ↓
Process B
   ↓
Process C
   ↓
Process A
```

So the CPU can share execution time among multiple runnable tasks.

---

# 31. Is Context Switching Free?

**No.**

Context switching has overhead.

During the switch, the CPU is doing work to save and restore execution state rather than directly executing the application's useful instructions.

There can also be additional effects on caches, TLBs, branch prediction, and other processor resources.

Therefore:

> Too many context switches can reduce performance.

---

# 32. What Causes a Context Switch?

Common causes include:

### 1. Time Slice Expires

In a preemptive scheduler:

```text
Process A gets CPU
       ↓
Time slice expires
       ↓
Scheduler chooses another process
       ↓
Context switch
```

---

### 2. Process Blocks for I/O

```text
Process A
   ↓
Requests I/O
   ↓
A becomes waiting
   ↓
Scheduler chooses B
   ↓
Context switch
```

---

### 3. Higher-Priority Runnable Process

Depending on the scheduling policy, a newly runnable higher-priority process can cause the currently running process to be preempted.

---

### 4. Interrupts / Kernel Scheduling Events

Hardware interrupts and other kernel events can cause the scheduler to reconsider which task should run.

> An interrupt does **not automatically mean a context switch**. The OS may handle an interrupt and then continue running the same process.

---

# 33. Process Synchronization

Suppose two processes access the same shared resource.

For example:

```text
Shared bank account = ₹1000
```

Process A:

```text
Withdraw ₹700
```

Process B:

```text
Withdraw ₹500
```

If both processes read and update the balance at the wrong time, the result can become incorrect.

This is a synchronization problem.

---

# 34. Why Do We Need Synchronization?

When multiple processes/threads access shared data, we need to control their execution so that operations happen safely.

Example:

```text
Shared Resource
      ↑
      |
+-----+-----+
|           |
Process A  Process B
```

The OS and synchronization mechanisms help prevent incorrect concurrent access.

---

# 35. Race Condition

A **race condition** occurs when the result depends on the timing/order of concurrent operations.

Example:

Initial:

```text
counter = 10
```

Two processes both execute:

```text
counter = counter + 1
```

Conceptually, both might read:

```text
10
```

Then both calculate:

```text
11
```

And both write:

```text
11
```

Expected:

```text
12
```

Actual:

```text
11
```

This is a classic race-condition example.

---

# 36. Critical Section

A **critical section** is a part of a program where shared data/resource is accessed.

Example:

```text
lock

critical section
    ↓
modify shared data

unlock
```

Only the appropriate process/thread should enter the critical section at a time when exclusive access is required.

Common synchronization mechanisms include:

* Mutexes
* Semaphores
* Monitors
* Atomic operations
* Condition variables

---

# 37. Inter-Process Communication (IPC)

Processes sometimes need to communicate with each other.

This is called:

# Inter-Process Communication (IPC)

For example:

```text
Process A
    |
    | Message
    ↓
Process B
```

Common IPC mechanisms include:

### 1. Pipes

One process can send data to another through a pipe.

```text
Process A
   ↓
 Pipe
   ↓
Process B
```

---

### 2. Message Queues

Processes communicate by sending messages.

```text
Process A
   ↓
Message Queue
   ↓
Process B
```

---

### 3. Shared Memory

Two or more processes can access a shared memory region, with synchronization used to safely coordinate access.

```text
Process A ──┐
            ↓
       Shared Memory
            ↑
Process B ──┘
```

Shared memory can be fast because processes can directly access the shared region after it is established.

But synchronization becomes especially important.

---

# 38. Deadlock

Another important process-management problem is **deadlock**.

Imagine:

```text
Process A has Resource 1
Process B has Resource 2
```

Now:

```text
A wants Resource 2
B wants Resource 1
```

So:

```text
A → waiting for B
B → waiting for A
```

Neither can continue.

This is a **deadlock**.

---

# 39. Simple Deadlock Example

Imagine two people:

```text
Person A has Pen
Person B has Paper
```

A says:

> "Give me the paper."

B says:

> "Give me the pen."

Neither gives up what they have.

So both wait forever.

This is similar to deadlock.

---

# 40. Four Conditions for Deadlock

A classic interview topic is the **Coffman conditions**.

Deadlock can occur when all four conditions hold:

### 1. Mutual Exclusion

At least one resource cannot be shared simultaneously.

```text
Only one process can use it at a time.
```

### 2. Hold and Wait

A process holds one resource while waiting for another.

```text
Holding A
Waiting for B
```

### 3. No Preemption

A resource cannot simply be forcibly taken away from the process holding it.

### 4. Circular Wait

There is a circular chain of waiting:

```text
P1 → P2
↑     ↓
P4 ← P3
```

More formally:

```text
P1 waits for P2
P2 waits for P3
P3 waits for P4
P4 waits for P1
```

If all four conditions exist, deadlock can occur.

---

# 41. Process Management — Big Picture

You can remember process management as:

```text
                    PROCESS MANAGEMENT
                           |
       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
    Creation           Scheduling         Termination
       |                   |
       ↓                   ↓
     PCB              CPU Allocation
                           |
                           ↓
                    Context Switching
                           |
              ┌────────────┴────────────┐
              ↓                         ↓
       Synchronization                IPC
              |
              ↓
         Shared Resources
              |
              ↓
           Deadlocks
```

---

# 42. Complete Process Lifecycle

Let's put everything together.

Suppose you open a program:

```text
             Program
                |
                ↓
        Process is created
                |
                ↓
              NEW
                |
                ↓
             READY
                |
                ↓
           Scheduler
                |
                ↓
            RUNNING
           /       \
          /         \
       I/O           Exit
        ↓              ↓
    WAITING        TERMINATED
        |
        | I/O complete
        ↓
      READY
```

During execution, the process may repeatedly move:

```text
READY → RUNNING → WAITING → READY → RUNNING
```

And eventually:

```text
RUNNING → TERMINATED
```

---

# 43. How Process Management Helps CPU Utilization

Consider two processes.

### Process A

```text
CPU → I/O → CPU → I/O
```

### Process B

```text
CPU → CPU → CPU → CPU
```

If A waits for I/O:

```text
A → Waiting
```

The OS can run B:

```text
A → Waiting
B → Running
```

When A's I/O finishes:

```text
A → Ready
```

Later the scheduler can run A again.

This keeps the CPU busy instead of unnecessarily waiting.

---

# 44. Process Management vs CPU Scheduling

These terms are related but not identical.

### Process Management

The broader concept:

```text
Create
Terminate
Schedule
Synchronize
Communicate
Manage resources
Handle process states
```

### CPU Scheduling

A specific part of process management:

```text
Decide which ready process gets CPU time.
```

So:

> **CPU scheduling is a part of process management.**

---

# 45. Process vs Thread

Another very common placement question.

A **process** is an execution environment with its own address space and resources.

A **thread** is a unit of execution within a process.

Conceptually:

```text
Process
  |
  +── Thread 1
  +── Thread 2
  +── Thread 3
```

Threads in the same process typically share:

```text
Code
Data
Heap
Address space
```

But each thread has its own execution state such as:

```text
Program Counter
Registers
Stack
```

### Simple analogy

```text
Process = House
Threads = People working inside the house
```

They share many things in the house but each person has their own current activity.

---

# 46. Important Placement Comparison

| Process                                  | Thread                                                                                  |
| ---------------------------------------- | --------------------------------------------------------------------------------------- |
| Larger execution unit                    | Smaller execution unit                                                                  |
| Has its own address space                | Threads of same process share address space                                             |
| Process creation is generally heavier    | Thread creation is generally lighter                                                    |
| Communication can require IPC mechanisms | Threads can communicate through shared memory directly, but synchronization is required |
| Failure/isolation is generally stronger  | Threads within a process are less isolated from each other                              |

> Exact costs and behavior depend on the operating system.

---

# 47. Preemptive vs Non-Preemptive Scheduling

### Non-Preemptive

Once a process gets the CPU, it generally keeps it until it:

* finishes, or
* blocks/waits.

Conceptually:

```text
P1 → Running
       |
       ↓
   finishes
       |
       ↓
P2 → Running
```

---

### Preemptive

The OS can interrupt a running process and give the CPU to another runnable process.

```text
P1 → Running
       ↓
Time slice expires
       ↓
P1 → Ready

P2 → Running
```

Modern general-purpose operating systems generally use preemptive scheduling.

---

# 48. Scheduler vs Dispatcher

This distinction can appear in interviews.

### Scheduler

Chooses **which process/thread should run next**.

```text
Ready Queue
   |
   ↓
Scheduler
   |
   ↓
Selected process
```

### Dispatcher

Performs the steps needed to transfer CPU control to the selected process/thread.

This can include:

* Context switching
* Switching to the appropriate execution mode
* Jumping to the appropriate instruction

So:

```text
Scheduler → Makes the decision

Dispatcher → Carries out the switch
```

---

# 49. Long-Term, Short-Term and Medium-Term Schedulers

Traditional OS theory describes three types.

### Long-Term Scheduler

Controls which jobs/processes are admitted into the system for execution.

It affects the degree of multiprogramming.

---

### Short-Term Scheduler

Chooses which ready process/thread gets the CPU next.

This is the scheduler most directly associated with CPU scheduling.

---

### Medium-Term Scheduler

May temporarily suspend processes and later bring them back into memory/execution.

This is associated with **swapping/suspension** in traditional OS designs.

---

# 50. Interview Questions You Should Know

## Q1. What is a process?

**Answer:**

> A process is a program in execution, together with its execution state and resources managed by the operating system.

---

## Q2. What is PCB?

**Answer:**

> PCB stands for Process Control Block. It is an OS data structure that stores information needed to manage a process, such as PID, process state, program counter, CPU registers, scheduling information, memory information, and I/O information.

---

## Q3. What is context switching?

**Answer:**

> Context switching is the process of saving the execution state of one process/thread and restoring the state of another so the CPU can switch execution between them.

---

## Q4. Is context switching expensive?

**Answer:**

> It has overhead because the OS must save and restore execution state, and switching can also affect processor caches and other hardware state. Therefore excessive context switching can hurt performance.

---

## Q5. What is the difference between CPU-bound and I/O-bound processes?

**Answer:**

> A CPU-bound process spends relatively more time performing computation, while an I/O-bound process spends relatively more time waiting for I/O operations.

---

## Q6. Why does an OS switch processes when one process performs I/O?

**Answer:**

> Because the process cannot continue until the I/O completes. The OS can schedule another ready process so that the CPU can continue doing useful work instead of remaining idle.

---

## Q7. What is a race condition?

**Answer:**

> A race condition occurs when the result of concurrent execution depends on the timing or ordering of operations accessing shared state.

---

## Q8. What is process synchronization?

**Answer:**

> Process synchronization coordinates concurrent processes or threads so that shared resources are accessed safely and operations occur in a controlled manner.

---

## Q9. What is IPC?

**Answer:**

> IPC, or Inter-Process Communication, refers to mechanisms that allow processes to exchange data and coordinate with one another, such as pipes, message queues, and shared memory.

---

## Q10. What is deadlock?

**Answer:**

> Deadlock is a situation where a group of processes cannot proceed because each is waiting for a resource or event that depends on another process in the group.

---

# 51. Frequently Confused Concepts

### Process ≠ Program

```text
Program = passive instructions
Process = program executing
```

---

### Process Management ≠ Process Scheduling

```text
Process Management
        |
        +── Scheduling
        +── Creation
        +── Termination
        +── Synchronization
        +── IPC
        +── Deadlock handling
```

---

### Waiting ≠ Ready

**Ready:**

> "I can run, but I need the CPU."

**Waiting:**

> "I cannot run yet because I am waiting for an event/resource."

This distinction is extremely important.

---

### Context Switch ≠ Process Creation

Creating a process means setting up a new process.

Context switching means changing CPU execution from one existing execution context to another.

---

# 52. Placement Cheat Sheet

Memorize these:

```text
Process
= Program in execution

PCB
= Information used by OS to manage a process

PID
= Identifier of a process

Ready
= Waiting for CPU

Running
= Currently executing on CPU

Waiting/Blocked
= Waiting for an event/I/O

Terminated
= Finished execution

CPU-bound
= More computation

I/O-bound
= More I/O waiting

CPU Scheduling
= Selecting which runnable process gets CPU

Context Switch
= Save one execution context + restore another

IPC
= Communication between processes

Synchronization
= Safe coordination of concurrent execution

Race Condition
= Result depends on timing/order

Deadlock
= Processes wait indefinitely for one another/resources
```

---

# 53. One-Minute Revision

If an interviewer asks:

> **"Explain Process Management."**

A good structured answer is:

> **Process management is an operating-system function responsible for managing processes throughout their lifecycle. It includes process creation and termination, CPU scheduling, synchronization, inter-process communication, and handling issues such as deadlocks.**
>
> **A process moves through states such as New, Ready, Running, Waiting/Blocked, and Terminated. The OS maintains information about each process using a Process Control Block (PCB).**
>
> **When a running process cannot continue, for example because it is waiting for I/O, the OS can schedule another ready process. To switch CPU execution between processes, the OS performs a context switch by saving the current execution state and restoring another process's state.**
>
> **The overall goal is to use CPU and system resources efficiently while providing correct, responsive, and coordinated execution of processes.**

---

# 54. Final Mental Model

Think of the Operating System as a **manager of workers**.

```text
                 OPERATING SYSTEM
                        |
                        ↓
                 Process Manager
                        |
        ┌───────────────┼────────────────┐
        ↓               ↓                ↓
     Create          Schedule         Terminate
        |               |
        ↓               ↓
      PCB          CPU Allocation
                        |
                        ↓
                Context Switching
                        |
          ┌─────────────┴─────────────┐
          ↓                           ↓
    Synchronization                  IPC
          ↓
    Shared Resources
          ↓
       Deadlocks
```

And remember the most important flow:

```text
                 +------+
                 | NEW  |
                 +--+---+
                    |
                    ↓
               +---------+
               |  READY  | ←──────────────┐
               +----+----+                |
                    |                     |
                 CPU assigned              |
                    ↓                     |
               +---------+                 |
               | RUNNING |                 |
               +----+----+                 |
                    |                     |
          ┌─────────┼──────────┐          |
          ↓         ↓          ↓          |
        I/O       Preempt     Exit        |
          ↓         ↓          ↓          |
     +---------+  READY   TERMINATED      |
     | WAITING |                         |
     +----+----+                         |
          |                              |
      I/O complete ─────────────────────┘
```

**For placements, focus especially on:** **PCB, process states, CPU-bound vs I/O-bound, scheduling metrics, context switching, process vs thread, preemptive vs non-preemptive scheduling, IPC, race conditions, synchronization, critical sections, and deadlocks.**
