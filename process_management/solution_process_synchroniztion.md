# Solutions to Process Synchronization Problems

> **Process synchronization** is the set of techniques used by an operating system to safely coordinate multiple processes or threads that access shared resources.

When multiple processes access the same resource without proper coordination, problems such as **race conditions, data inconsistency, lost updates, deadlock, and starvation** can occur.

This chapter explains the major approaches to solving synchronization problems, starting from simple low-level techniques and moving toward practical OS-level mechanisms.

---

## Table of Contents

- [1. Why Do We Need Synchronization?](#1-why-do-we-need-synchronization)
- [2. The Basic Problem](#2-the-basic-problem)
- [3. Major Synchronization Approaches](#3-major-synchronization-approaches)
- [4. Approach 1: Disabling Interrupts](#4-approach-1-disabling-interrupts)
  - [4.1 How It Works](#41-how-it-works)
  - [4.2 Example](#42-example)
  - [4.3 Advantages](#43-advantages)
  - [4.4 Problems](#44-problems)
- [5. Approach 2: Locks](#5-approach-2-locks)
  - [5.1 Basic Idea](#51-basic-idea)
  - [5.2 Software-Based Locks](#52-software-based-locks)
  - [5.3 Peterson's Algorithm](#53-petersons-algorithm)
  - [5.4 Dekker's Algorithm](#54-dekkers-algorithm)
  - [5.5 Bakery Algorithm](#55-bakery-algorithm)
  - [5.6 Problems With Software Locks](#56-problems-with-software-locks)
  - [5.7 Hardware-Based Locks](#57-hardware-based-locks)
  - [5.8 Test-and-Set](#58-test-and-set)
  - [5.9 Compare-and-Swap](#59-compare-and-swap)
  - [5.10 Spinlock](#510-spinlock)
- [6. Mutex](#6-mutex)
- [7. Semaphore](#7-semaphore)
- [8. Monitor](#8-monitor)
- [9. Mutex vs Semaphore vs Monitor](#9-mutex-vs-semaphore-vs-monitor)
- [10. Busy Waiting vs Blocking](#10-busy-waiting-vs-blocking)
- [11. Complete Evolution of Synchronization](#11-complete-evolution-of-synchronization)
- [12. Real-World Examples](#12-real-world-examples)
- [13. Important Interview Questions](#13-important-interview-questions)
- [14. Technical Explanation](#14-technical-explanation)
- [15. Placement Cheat Sheet](#15-placement-cheat-sheet)

---

# 1. Why Do We Need Synchronization?

Suppose we have:

```text
counter = 0
```

Two threads want to increment it:

```text
Thread 1 → counter++
Thread 2 → counter++
```

We expect:

```text
0 + 1 + 1 = 2
```

But `counter++` is conceptually:

```text
READ
  ↓
ADD 1
  ↓
WRITE
```

The operations can overlap:

```text
Thread 1              Thread 2

Read 0
                      Read 0

Add 1
                      Add 1

Write 1
                      Write 1
```

Final result:

```text
counter = 1
```

Expected:

```text
counter = 2
```

This is a **race condition**.

So we need synchronization.

---

# 2. The Basic Problem

The basic synchronization problem looks like this:

```text
                 Shared Resource
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
         Process P1           Process P2
             │                   │
             └─────────┬─────────┘
                       ↓
               Critical Section
                       │
                       ↓
                Synchronization
```

The goal is to make sure that concurrent processes do not incorrectly interfere with each other.

A simple requirement is:

```text
Only one process at a time
        ↓
Critical Section
```

But real synchronization is more than just mutual exclusion. Good solutions also need to consider:

- **Mutual exclusion**
- **Progress**
- **Bounded waiting**
- **CPU efficiency**
- **Fairness**
- **Blocking and waking**
- **Multiprocessor support**

---

# 3. Major Synchronization Approaches

The approaches can be understood as an evolution:

```text
                 Synchronization
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
 Interrupt          Locks       OS Mechanisms
 Disable
                         │
                 ┌───────┴────────┐
                 ↓                ↓
              Software         Hardware
                Locks            Locks
                                  │
                         ┌────────┼────────┐
                         ↓        ↓        ↓
                        TSL      CAS    Spinlock
                                          
                                  ↓
                                Mutex
                                  │
                    ┌─────────────┴─────────────┐
                    ↓                           ↓
               Semaphore                    Monitor
```

The historical progression is roughly:

```text
Disable Interrupts
       ↓
Software Algorithms
       ↓
Hardware Atomic Instructions
       ↓
Locks / Spinlocks
       ↓
Mutexes
       ↓
Semaphores / Monitors
```

The important point is that modern operating systems combine hardware atomic primitives with OS scheduling/blocking mechanisms rather than relying on one technique alone.

---

# 4. Approach 1: Disabling Interrupts

## 4.1 How It Works

One early approach is:

> **Disable interrupts before entering the critical section.**

Why?

On a single CPU, if interrupts are disabled, the current execution cannot be interrupted by normal hardware interrupts.

Conceptually:

```text
Disable Interrupts
        ↓
Critical Section
        ↓
Enable Interrupts
```

Example:

```text
Process P1
    ↓
Disable Interrupts
    ↓
Update Shared Data
    ↓
Enable Interrupts
```

Since the CPU isn't interrupted during the protected section, another process cannot be scheduled through the usual interrupt-driven preemption mechanism.

---

# 4.2 Example

Suppose:

```text
balance = ₹100
```

P1 wants to update it.

```text
Disable Interrupts

balance = balance + 10

Enable Interrupts
```

On a single CPU, P1 can execute the protected operation without a timer interrupt preempting it.

---

# 4.3 Advantages

### Simple

The basic idea is easy to understand:

```text
Disable
  ↓
Critical Section
  ↓
Enable
```

### Useful in Some Kernel Code

Operating systems may disable interrupts briefly when manipulating data that must not be interrupted on the current CPU, depending on the specific kernel subsystem.

---

# 4.4 Problems

This approach has serious limitations.

### Problem 1: Multiprocessor Systems

Suppose we have two CPUs:

```text
CPU 1                    CPU 2

Interrupts disabled      Running normally
     │                        │
     ↓                        ↓
Critical Section         Access shared data
```

Disabling interrupts on CPU 1 does **not** stop CPU 2.

Therefore:

```text
Disable interrupts on CPU 1
        ≠
Stop all CPUs
```

---

### Problem 2: Dangerous

If interrupts are disabled and never re-enabled:

```text
Disable Interrupts
       ↓
Something goes wrong
       ↓
Never Enable
       ↓
System can become unresponsive
```

---

### Problem 3: User Programs Cannot Be Trusted With It

A user program should not normally be given unrestricted ability to disable hardware interrupts.

Otherwise:

```text
User Program
     ↓
Disable Interrupts
     ↓
Never Enable
     ↓
Operating System becomes unable to respond normally
```

Therefore, this is mainly a **privileged kernel-level technique**, not a normal application synchronization mechanism.

---

### Interview Point

> **Disabling interrupts is not a general-purpose synchronization mechanism for modern multiprocessor systems.**

---

# 5. Approach 2: Locks

A **lock** is a synchronization mechanism used to control access to a shared resource.

The basic idea is:

```text
Acquire Lock
     ↓
Critical Section
     ↓
Release Lock
```

Example:

```text
lock();

counter++;

unlock();
```

Imagine a room with one key:

```text
        Room
         │
    One Key Only
         │
 ┌───────┴───────┐
 ↓               ↓
P1              P2
Gets Key        Waits
 ↓
Uses Room
 ↓
Returns Key
                 ↓
               P2
```

Here:

```text
Room       → Shared Resource
Key        → Lock
Inside     → Critical Section
Waiting    → Other Process
```

---

# 5.1 Basic Idea

Without a lock:

```text
P1 ───────┐
          ├──→ Shared Data
P2 ───────┘
```

With a lock:

```text
P1 → Acquire Lock → Shared Data → Release
                                      ↓
P2 → Acquire Lock → Shared Data → Release
```

The lock provides controlled access.

---

# 5.2 Software-Based Locks

Software-based synchronization algorithms use shared variables and carefully designed protocols.

They attempt to solve the critical-section problem without relying directly on special hardware atomic instructions.

Important examples:

```text
1. Peterson's Algorithm
2. Dekker's Algorithm
3. Bakery Algorithm
```

These are historically important and are excellent for understanding synchronization concepts.

---

# 5.3 Peterson's Algorithm

**Peterson's Algorithm** is a classic software solution for mutual exclusion between **two processes**.

It uses:

```text
flag[]
turn
```

Conceptually:

```text
flag[i] = true
turn = j

while (flag[j] && turn == j)
    wait
```

Meaning:

```text
flag[i] = true
```

means:

> "I want to enter."

And:

```text
turn = j
```

means:

> "I'll give the other process priority if we both want to enter."

Conceptually:

```text
P0                          P1

Wants to enter              Wants to enter
     │                            │
 flag[0] = true              flag[1] = true
     │                            │
 turn = 1                    turn = 0
     │                            │
     └──────────┬─────────────────┘
                ↓
         One waits, one enters
```

### Important

Peterson's algorithm is mainly a **two-process theoretical solution**.

It is important for OS exams/interviews, but it is not how modern production synchronization is normally implemented.

---

# 5.4 Dekker's Algorithm

**Dekker's Algorithm** is another early software solution for mutual exclusion between two processes.

It combines:

```text
Flags
+
Turn
```

to coordinate which process gets access.

The basic idea is:

```text
P0 wants to enter
       ↓
P1 also wants to enter
       ↓
Use turn to decide
       ↓
One enters
       ↓
Other waits
```

Dekker's algorithm is historically important because it demonstrates that mutual exclusion can be constructed using software logic and shared variables under appropriate assumptions.

---

# 5.5 Bakery Algorithm

The **Bakery Algorithm** extends the idea to multiple processes.

It is inspired by taking a number at a bakery.

Imagine:

```text
Customer A → Token 5
Customer B → Token 6
Customer C → Token 7
```

The smallest number gets served first.

Similarly:

```text
Process P1 → Ticket 5
Process P2 → Ticket 6
Process P3 → Ticket 7
```

The process with the smallest ticket enters first.

Conceptually:

```text
P1 → Ticket 5
P2 → Ticket 3
P3 → Ticket 8
P4 → Ticket 4

Order:

P2 → P4 → P1 → P3
```

This provides a conceptual approach to:

- Mutual exclusion
- Fair ordering
- Multiple processes

---

# 5.6 Problems With Software Locks

Software-only algorithms have important limitations.

### 1. Complex

For example:

```text
Peterson → relatively simple for 2
Bakery   → more complex for many
```

---

### 2. Busy Waiting

A process may repeatedly check:

```c
while (condition)
    ;
```

This consumes CPU time.

```text
CPU
 │
 ├── Check
 ├── Check
 ├── Check
 ├── Check
 └── Check
```

No useful work is being done.

---

### 3. Hardware and Memory-Model Issues

Modern multiprocessors have:

- CPU caches
- Compiler optimizations
- Out-of-order execution
- Weak/relaxed memory ordering

Therefore, real-world synchronization needs appropriate hardware-supported atomic operations and memory-ordering guarantees.

This is one reason modern systems rely heavily on hardware primitives rather than classic textbook software-only algorithms.

---

# 5.7 Hardware-Based Locks

Modern CPUs provide **atomic instructions** that allow operating systems and libraries to build synchronization primitives.

Important examples:

```text
Test-and-Set
Compare-and-Swap
Exchange
Fetch-and-Add
```

The key word is:

> **Atomic**

---

# 5.8 What Does Atomic Mean?

**Atomic** means that an operation is performed as one indivisible synchronization action.

For example, suppose we want to acquire a lock.

Without an atomic operation:

```text
Check lock
     ↓
Set lock
```

Two processes could do:

```text
P1 → Check → Free
P2 → Check → Free

P1 → Set Locked
P2 → Set Locked
```

Both may believe they acquired the lock.

That's bad.

With an atomic operation:

```text
Check + Set
     ↓
One indivisible operation
```

Only one can successfully acquire the lock.

---

# 5.9 Test-and-Set

**Test-and-Set (TAS)** is a hardware-supported atomic operation.

Conceptually:

```text
old = lock

lock = true

return old
```

But the read and write happen atomically.

Suppose:

```text
lock = false
```

P1 performs Test-and-Set:

```text
P1 → sees false
P1 → sets true
P1 → gets lock
```

P2 tries:

```text
P2 → sees true
P2 → does not get lock
```

Conceptually:

```text
       Lock = FREE

          ↓
       P1 TAS
          ↓
     Lock = BUSY
          ↓
        P1 enters

P2 TAS → sees BUSY → waits
```

---

# 5.10 Compare-and-Swap

**Compare-and-Swap (CAS)** is another important atomic primitive.

Conceptually:

```text
CAS(address, expected, newValue)
```

It means:

```text
If current value == expected
        ↓
change it to newValue
Otherwise
        ↓
do nothing / report failure
```

Example:

```text
Current value = 0

Expected = 0
New value = 1
```

CAS:

```text
0 == 0
   ↓
Change 0 → 1
```

Success.

But if:

```text
Current value = 1

Expected = 0
New value = 1
```

Then:

```text
1 != 0
   ↓
CAS fails
```

CAS is widely used to implement lock-free algorithms and synchronization primitives.

---

# 5.11 Spinlock

A **spinlock** is a lock where a thread repeatedly checks whether the lock has become available.

Conceptually:

```c
while (lock_is_busy) {
    // keep checking
}
```

Then:

```text
Lock becomes free
      ↓
Thread acquires it
      ↓
Critical Section
```

The thread is **busy waiting**.

---

## Why Is It Called a Spinlock?

Because the CPU keeps "spinning" while checking the lock.

```text
CPU:

Check
 ↓
Check
 ↓
Check
 ↓
Check
 ↓
Lock free!
 ↓
Enter
```

---

## When Are Spinlocks Useful?

They can be useful when:

```text
Expected waiting time is very short
```

For example, low-level kernel code may use spinlocks where sleeping is inappropriate or where the protected section is extremely short.

---

## Problem With Spinlocks

If the lock remains unavailable for a long time:

```text
Thread
  ↓
Spin
  ↓
Spin
  ↓
Spin
  ↓
Spin
```

CPU cycles are wasted.

So:

```text
Short wait → Spinlock can be useful

Long wait → Blocking is generally better
```

---

# 6. Mutex

**Mutex** stands for:

> **Mutual Exclusion**

A mutex is a higher-level synchronization primitive used to protect a critical section.

The basic pattern:

```text
lock(mutex)

    // Critical Section

unlock(mutex)
```

Example:

```c
lock(mutex);

balance = balance + 100;

unlock(mutex);
```

---

## How Is a Mutex Different From a Spinlock?

### Spinlock

```text
Lock unavailable
      ↓
Keep checking
      ↓
CPU keeps running
```

### Mutex

```text
Lock unavailable
      ↓
Thread can block/sleep
      ↓
CPU can run another thread
```

Conceptually:

```text
             Lock unavailable
                    │
              ┌─────┴─────┐
              ↓           ↓
          Spinlock      Mutex
              │           │
          Busy wait    Block/Wait
              │           │
          CPU used     CPU available
```

A real mutex implementation may use spinning briefly and then block, depending on the OS/runtime.

---

# 7. Semaphore

A **semaphore** is a synchronization primitive based on a counter.

Think of a semaphore as a number of available **permits**.

For example:

```text
3 printers available
```

We can have:

```text
Semaphore = 3
```

Three processes can acquire permits.

```text
P1 → acquire → 2
P2 → acquire → 1
P3 → acquire → 0
P4 → acquire → WAIT
```

When one process finishes:

```text
P1 → release → 1
```

Now another waiting process can proceed.

---

# 7.1 Semaphore Operations

Two classical operations are:

```text
wait()
signal()
```

They are also commonly called:

```text
P() / V()
down() / up()
acquire() / release()
```

depending on the system/textbook.

Conceptually:

### `wait()`

```text
If permit available
    ↓
Take permit
Else
    ↓
Wait/block
```

### `signal()`

```text
Return permit
    ↓
Wake an appropriate waiter if necessary
```

---

# 7.2 Binary Semaphore

A binary semaphore has two logical states:

```text
0 → unavailable
1 → available
```

It can be used for mutual exclusion in some designs.

However:

> **A binary semaphore is not exactly the same thing as a mutex.**

A mutex generally has ownership semantics: the thread that locks it is expected to unlock it.

A semaphore is primarily a signaling/counting mechanism and generally does not have the same ownership rule.

---

# 7.3 Counting Semaphore

A counting semaphore can represent multiple available resources.

Example:

```text
5 identical resources

Semaphore = 5
```

Then:

```text
P1 → acquire → 4
P2 → acquire → 3
P3 → acquire → 2
P4 → acquire → 1
P5 → acquire → 0
P6 → wait
```

When a resource is released:

```text
signal()
```

the count becomes available again.

---

# 8. Monitor

A **monitor** is a higher-level synchronization construct that combines:

```text
Shared Data
+
Operations
+
Mutual Exclusion
+
Condition Variables
```

Think of it as a controlled room:

```text
                 MONITOR
        ┌─────────────────────┐
        │                     │
        │    Shared Data      │
        │                     │
        │    Functions        │
        │                     │
        │    Synchronization  │
        │                     │
        └─────────────────────┘
                  ↑
                  │
            Controlled Access
```

The monitor automatically provides mutual exclusion for its protected operations.

---

# 8.1 Condition Variables

A monitor can use **condition variables** when a thread needs to wait for some condition.

Example:

```text
Producer
   ↓
Adds item
   ↓
Consumer can continue
```

If the buffer is empty:

```text
Consumer
   ↓
Buffer empty
   ↓
wait()
```

Later:

```text
Producer adds item
       ↓
signal()
       ↓
Consumer wakes
```

So:

```text
wait()   → Sleep until condition may be true
signal() → Notify a waiting thread
```

The exact semantics vary by monitor implementation.

---

# 8.2 Simple Monitor Example

Imagine a shared queue:

```text
Queue
 ├── A
 ├── B
 └── C
```

Producer:

```text
Add item
```

Consumer:

```text
Remove item
```

The monitor can ensure that operations on the queue are synchronized.

Conceptually:

```text
monitor Queue {

    add(item) {
        // safely add item
    }

    remove() {
        // safely remove item
    }
}
```

Multiple threads cannot simultaneously execute conflicting monitor operations in a way that violates the monitor's mutual exclusion guarantee.

---

# 9. Mutex vs Semaphore vs Monitor

This is **very important for placements**.

| Feature | Mutex | Semaphore | Monitor |
|---|---|---|---|
| Main purpose | Mutual exclusion | Counting/signaling | High-level synchronization |
| Ownership | Usually yes | Generally no ownership requirement | Managed by monitor |
| Can represent multiple resources? | Usually no | Yes | Can encapsulate such logic |
| Blocking | Usually possible | Usually possible | Built into abstraction |
| Condition variables | Separate/associated | Not inherently | Commonly included |
| Abstraction level | Medium | Medium | High |
| Common use | Protect critical section | Resource counting/signaling | Structured shared-object synchronization |

### Easy memory trick

```text
Mutex
  ↓
"One owner"

Semaphore
  ↓
"How many permits?"

Monitor
  ↓
"Protected shared object"
```

---

# 10. Busy Waiting vs Blocking

This is one of the most important concepts in synchronization.

## Busy Waiting

The thread keeps checking:

```text
while (lock_busy) {
    // keep checking
}
```

CPU:

```text
Check
Check
Check
Check
Check
```

CPU time is consumed even though the thread isn't doing useful work.

This is commonly associated with:

```text
Spinlocks
```

---

# 10.1 Blocking

Instead of continuously checking:

```text
Lock unavailable
      ↓
Block / Sleep
      ↓
CPU runs another thread
      ↓
Lock becomes available
      ↓
Wake thread
```

This is commonly used by:

```text
Mutexes
Semaphores
Condition variables
```

---

# 10.2 Busy Waiting vs Blocking

| Busy Waiting | Blocking |
|---|---|
| Repeatedly checks condition | Sleeps/blocks |
| Uses CPU while waiting | CPU can run another task |
| Good for very short waits | Better for longer waits |
| Common with spinlocks | Common with mutexes/semaphores |
| Lower waiting overhead in some short cases | Scheduling/blocking has overhead |

### Important

Blocking is not always automatically faster.

If a lock will become free in a few CPU cycles, spinning briefly can be cheaper than putting a thread to sleep and later waking it.

Modern systems may therefore use **hybrid strategies**.

---

# 11. Complete Evolution of Synchronization

Now connect everything together.

## Stage 1: Disable Interrupts

```text
Disable Interrupts
        ↓
Critical Section
        ↓
Enable Interrupts
```

### Main problem:

```text
Doesn't work as a general solution on multiprocessors
```

---

## Stage 2: Software Algorithms

Examples:

```text
Peterson
Dekker
Bakery
```

Concept:

```text
Shared Variables
       ↓
Software Protocol
       ↓
Mutual Exclusion
```

### Main problems:

```text
Complexity
Busy Waiting
Limited practicality
```

---

## Stage 3: Hardware Atomic Operations

Examples:

```text
Test-and-Set
Compare-and-Swap
Exchange
Fetch-and-Add
```

Concept:

```text
CPU provides atomic primitive
          ↓
Build synchronization
```

---

## Stage 4: Spinlocks

```text
Atomic Hardware Instruction
          ↓
Spinlock
          ↓
Busy Waiting
```

Good for:

```text
Very short critical sections
```

---

## Stage 5: Mutex

```text
Lock
 ↓
If unavailable → Block
 ↓
Wake when available
```

Better when waiting may take longer.

---

## Stage 6: Semaphores

```text
Counter
  ↓
Acquire / Wait
  ↓
Release / Signal
```

Useful for:

```text
Resource counting
Synchronization
Signaling
```

---

## Stage 7: Monitors

```text
Shared Data
     +
Operations
     +
Mutual Exclusion
     +
Condition Variables
```

Higher-level and easier to use safely.

---

# 12. Real-World Examples

## 12.1 Bank Account

Suppose:

```text
Balance = ₹10,000
```

Two transactions:

```text
Transaction A → Withdraw ₹6,000
Transaction B → Withdraw ₹6,000
```

The operation:

```text
Check balance
     +
Update balance
```

needs proper synchronization/transaction control.

Possible solution:

```text
Acquire appropriate lock/transaction
        ↓
Check balance
        ↓
Update balance
        ↓
Commit/release
```

---

# 12.2 Ticket Booking

One seat:

```text
Seat A1 → Available
```

Two users:

```text
User A → Book A1
User B → Book A1
```

The system must ensure that checking availability and reserving the seat are coordinated correctly.

Possible mechanisms include:

```text
Database transaction
Row-level lock
Optimistic concurrency control
Atomic update
```

The exact implementation depends on the system.

---

# 12.3 Producer-Consumer

Suppose we have:

```text
Producer → Buffer → Consumer
```

The producer adds items.

The consumer removes items.

Problems:

```text
Producer → Buffer full
Consumer → Buffer empty
```

Synchronization is required.

Semaphores and condition variables are classic solutions.

Conceptually:

```text
          Buffer
        ┌─────────┐
Producer│ A B C   │Consumer
   ────→│         │────→
        └─────────┘
```

---

# 12.4 Shared File

Two processes write:

```text
Process A ──┐
            ├──→ data.txt
Process B ──┘
```

Without proper coordination:

```text
Corrupted/inconsistent data
```

A suitable locking or file-coordination mechanism may be required.

---

# 13. Important Interview Questions

## Q1. What are the major approaches to process synchronization?

A useful way to organize them is:

```text
1. Interrupt disabling
2. Software-based synchronization algorithms
3. Hardware atomic instructions
4. Locks / spinlocks
5. Mutexes
6. Semaphores
7. Monitors
```

Modern operating systems combine several of these layers.

---

## Q2. Why can't we simply disable interrupts?

Because:

1. It is not suitable for general user programs.
2. It does not provide general mutual exclusion across multiple CPUs.
3. It can make the system unresponsive if misused.
4. It is a privileged kernel-level technique.

---

## Q3. What is a software lock?

A synchronization algorithm implemented using shared variables and software logic.

Examples:

```text
Peterson
Dekker
Bakery
```

---

## Q4. What is a hardware lock?

More precisely, hardware provides **atomic primitives** that can be used to implement locks.

Examples:

```text
Test-and-Set
Compare-and-Swap
Exchange
Fetch-and-Add
```

---

## Q5. What is Test-and-Set?

An atomic hardware operation that reads the old value of a memory location and sets it to a new value, typically "locked", as one indivisible operation.

---

## Q6. What is Compare-and-Swap?

CAS atomically changes a memory location only if its current value equals an expected value.

Conceptually:

```text
if (*address == expected)
    *address = newValue;
```

with the comparison and update performed atomically.

---

## Q7. What is a spinlock?

A lock where the waiting thread repeatedly checks whether the lock has become available.

```text
while (locked)
    spin;
```

It is useful when the expected waiting period is very short.

---

## Q8. What is a mutex?

A mutual-exclusion synchronization primitive that protects a critical section.

If unavailable, a thread can generally block instead of continuously consuming CPU while waiting.

---

## Q9. What is a semaphore?

A synchronization primitive that maintains a count representing permits/resources and supports operations such as `wait` and `signal`.

It can be used for:

```text
Resource counting
Synchronization
Signaling
Mutual exclusion in certain designs
```

---

## Q10. What is a monitor?

A high-level synchronization construct that encapsulates shared data and operations while providing mutual exclusion and typically condition variables for coordination.

---

# 14. Technical Explanation

Now let's put everything together technically.

> **Process synchronization** is the coordination of concurrent execution contexts to ensure correct access to shared state and resources.

The critical-section problem requires a protocol satisfying:

```text
Mutual Exclusion
Progress
Bounded Waiting
```

Historically, solutions evolved from interrupt disabling and software algorithms toward hardware atomic instructions and OS-supported blocking primitives.

### Interrupt disabling

On a uniprocessor system, disabling interrupts can prevent the current execution from being preempted by interrupts. However, this does not provide general multiprocessor mutual exclusion and is therefore restricted to appropriate privileged kernel contexts.

### Software algorithms

Algorithms such as:

```text
Peterson
Dekker
Bakery
```

demonstrate mutual exclusion using shared variables and software protocols. They are primarily educational/theoretical because practical modern systems need to account for hardware memory ordering and multiprocessor behavior.

### Hardware atomic primitives

Modern processors provide atomic operations such as:

```text
Test-and-Set
Compare-and-Swap
Exchange
Fetch-and-Add
```

These primitives form the low-level building blocks for synchronization mechanisms.

### Spinlocks

A spinlock uses atomic primitives to acquire a lock while the waiting thread repeatedly checks the lock state.

### Mutexes

A mutex provides mutual exclusion and can block a waiting thread rather than requiring it to continuously spin.

### Semaphores

A semaphore maintains a count and provides atomic wait/signal-style operations. It is useful for both resource counting and process/thread synchronization.

### Monitors

A monitor is a higher-level abstraction that encapsulates shared state, operations, mutual exclusion, and condition-based waiting.

---

# 15. Placement Cheat Sheet

## One-Line Definitions

| Topic | One-Line Definition |
|---|---|
| **Synchronization** | Coordinates concurrent processes/threads accessing shared resources |
| **Critical Section** | Code region accessing shared state that requires synchronization |
| **Interrupt Disable** | Temporarily prevents interrupt-driven preemption on a CPU |
| **Software Lock** | Synchronization using software algorithms and shared variables |
| **Peterson** | Two-process software mutual exclusion algorithm |
| **Dekker** | Early two-process software mutual exclusion algorithm |
| **Bakery** | Multi-process software algorithm inspired by ticket numbers |
| **Atomic Operation** | Indivisible synchronization operation |
| **Test-and-Set** | Atomically reads and sets a memory value |
| **CAS** | Atomically updates a value if it equals an expected value |
| **Spinlock** | Lock where the waiter repeatedly checks for availability |
| **Mutex** | Mutual-exclusion primitive, typically capable of blocking waiters |
| **Semaphore** | Counter-based synchronization primitive |
| **Monitor** | High-level synchronization abstraction with mutual exclusion and conditions |

---

# 16. Most Important Comparisons

## Spinlock vs Mutex

```text
Spinlock
   ↓
Busy Wait
   ↓
Good for very short waits

Mutex
   ↓
Can Block/Sleep
   ↓
Good when waiting may be longer
```

---

## Mutex vs Semaphore

```text
Mutex
 ↓
Usually one owner
 ↓
Protect critical section

Semaphore
 ↓
Counter/permits
 ↓
Resource counting + signaling
```

---

## Semaphore vs Monitor

```text
Semaphore
 ↓
Lower-level primitive
 ↓
wait() / signal()

Monitor
 ↓
Higher-level abstraction
 ↓
Shared data + operations + synchronization
```

---

## Software vs Hardware Synchronization

```text
Software
   ↓
Peterson / Dekker / Bakery
   ↓
Complex
   ↓
Mostly educational/historical

Hardware
   ↓
CAS / Test-and-Set / Exchange
   ↓
Atomic
   ↓
Foundation for modern synchronization
```

---

# 17. The Complete Mental Model

Keep this diagram in your notes:

```text
                  PROCESS SYNCHRONIZATION
                           │
                           ↓
                    Shared Resource
                           │
                           ↓
                    Critical Section
                           │
                           ↓
                  Need Safe Access
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ↓                ↓                ↓
    Disable Interrupts   Software        Hardware
                         Algorithms       Atomic Ops
                            │                │
                  ┌─────────┼──────┐    ┌────┴─────┐
                  ↓         ↓      ↓    ↓          ↓
               Peterson   Dekker  Bakery TAS       CAS
                                             │
                                             ↓
                                         Spinlock
                                             │
                                             ↓
                                           Mutex
                                             │
                              ┌──────────────┴──────────────┐
                              ↓                             ↓
                         Semaphore                      Monitor
```

---

# 18. The Most Important Conceptual Flow

For placements, understand this progression:

```text
Multiple Processes/Threads
          ↓
     Shared Resource
          ↓
    Concurrent Access
          ↓
      Race Condition
          ↓
 Need Synchronization
          ↓
 ┌────────┴────────┐
 ↓                 ↓
Lock            OS-level
                Primitives
 ↓                 ↓
Mutex          Semaphore
Spinlock       Monitor
 ↓
Hardware Atomic Instructions
 ↓
CAS / Test-and-Set
```

---

# 19. What You Should Remember for Placements

If the interviewer asks:

### "How can we solve the critical-section problem?"

Start with:

> A critical-section solution should satisfy **mutual exclusion, progress, and bounded waiting**. Synchronization can be implemented using software algorithms such as Peterson's or Bakery's algorithm, hardware atomic primitives such as Test-and-Set and Compare-and-Swap, and higher-level OS mechanisms such as mutexes, semaphores, and monitors.

Then explain the evolution:

```text
Interrupt Disable
       ↓
Software Algorithms
       ↓
Hardware Atomic Operations
       ↓
Spinlocks
       ↓
Mutexes
       ↓
Semaphores / Monitors
```

### The 7 terms you absolutely should know

```text
1. Critical Section
2. Mutual Exclusion
3. Progress
4. Bounded Waiting
5. Mutex
6. Semaphore
7. Monitor
```

And for deeper placement questions:

```text
Peterson
Dekker
Bakery
Test-and-Set
Compare-and-Swap
Spinlock
Busy Waiting
Blocking
Starvation
Deadlock
```

> **Core idea:** Synchronization is not simply about "stopping two processes from running together." It is about ensuring that concurrent execution accesses shared state according to the rules required for correctness, while also considering fairness and CPU efficiency.