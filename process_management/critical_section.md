# Critical Section in Process Synchronization

> A **critical section** is the part of a program where a process or thread accesses or modifies a **shared resource**.

When multiple processes or threads use the same resource, they must be coordinated properly. Otherwise, problems such as **race conditions, lost updates, and inconsistent data** can occur.

The main goal is:

> **Allow safe access to shared resources when multiple processes/threads execute concurrently.**

---

## 📌 Table of Contents

* [1. First, Understand the Problem](#1-first-understand-the-problem)
* [2. What Is a Critical Section?](#2-what-is-a-critical-section)
* [3. Why Do We Need a Critical Section?](#3-why-do-we-need-a-critical-section)
* [4. Critical Section and Race Condition](#4-critical-section-and-race-condition)
* [5. Simple Real-Life Example](#5-simple-real-life-example)
* [6. Structure of a Critical Section](#6-structure-of-a-critical-section)
* [7. Entry Section](#7-entry-section)
* [8. Critical Section](#8-critical-section)
* [9. Exit Section](#9-exit-section)
* [10. Remainder Section](#10-remainder-section)
* [11. Complete Example](#11-complete-example)
* [12. What Is the Critical Section Problem?](#12-what-is-the-critical-section-problem)
* [13. Requirement 1: Mutual Exclusion](#13-requirement-1-mutual-exclusion)
* [14. Why Is Mutual Exclusion Important?](#14-why-is-mutual-exclusion-important)
* [15. Requirement 2: Progress](#15-requirement-2-progress)
* [16. Simple Example of Progress](#16-simple-example-of-progress)
* [17. Requirement 3: Bounded Waiting](#17-requirement-3-bounded-waiting)
* [18. Starvation](#18-starvation)
* [19. Mutual Exclusion vs Progress vs Bounded Waiting](#19-mutual-exclusion-vs-progress-vs-bounded-waiting)
* [20. How Do We Protect a Critical Section?](#20-how-do-we-protect-a-critical-section)
* [21. Mutex](#21-mutex)
* [22. Semaphore](#22-semaphore)
* [23. Atomic Operations](#23-atomic-operations)
* [24. Monitor](#24-monitor)
* [25. Real-World Example 1: Bank Account](#25-real-world-example-1-bank-account)
* [26. Real-World Example 2: Ticket Booking](#26-real-world-example-2-ticket-booking)
* [27. Real-World Example 3: Printer Queue](#27-real-world-example-3-printer-queue)
* [28. Real-World Example 4: Shared File](#28-real-world-example-4-shared-file)
* [29. Critical Section vs Mutex](#29-critical-section-vs-mutex)
* [30. Critical Section vs Race Condition](#30-critical-section-vs-race-condition)
* [31. Critical Section vs Deadlock](#31-critical-section-vs-deadlock)
* [32. Critical Section and Context Switching](#32-critical-section-and-context-switching)
* [33. Can We Disable Interrupts?](#33-can-we-disable-interrupts)
* [34. Important Correction: "Only One Thread" Is Not Always the Whole Story](#34-important-correction-only-one-thread-is-not-always-the-whole-story)
* [35. Critical Section Problem — Technical View](#35-critical-section-problem--technical-view)
* [36. Why These Three Matter](#36-why-these-three-matter)
* [37. Placement Interview Questions](#37-placement-interview-questions)
* [38. Q5. What is Mutual Exclusion?](#38-q5-what-is-mutual-exclusion)
* [39. Q6. What is Progress?](#39-q6-what-is-progress)
* [40. Q7. What is Bounded Waiting?](#40-q7-what-is-bounded-waiting)
* [41. Q8. How can we implement a critical section?](#41-q8-how-can-we-implement-a-critical-section)
* [42. Q9. Is a critical section the same as a mutex?](#42-q9-is-a-critical-section-the-same-as-a-mutex)
* [43. Q10. Can a critical section contain multiple statements?](#43-q10-can-a-critical-section-contain-multiple-statements)
* [44. Q11. Can multiple readers be inside simultaneously?](#44-q11-can-multiple-readers-be-inside-simultaneously)
* [45. Q12. Does a critical section always mean a race condition exists?](#45-q12-does-a-critical-section-always-mean-a-race-condition-exists)
* [46. A Complete Mental Model](#46-a-complete-mental-model)
* [47. Complete Flow of a Critical Section](#47-complete-flow-of-a-critical-section)
* [48. Technical Explanation](#48-technical-explanation)
* [49. Critical Section — Placement Cheat Sheet](#49-critical-section--placement-cheat-sheet)

---

# 1. First, Understand the Problem

Suppose we have:

```text
balance = ₹100
```

Two threads are running:

```text
Thread 1 → Add ₹10
Thread 2 → Subtract ₹10
```

Both threads use the same `balance`.

If they access it without synchronization, they might interfere with each other.

```text
Thread 1 ─────┐
              ├──→ balance
Thread 2 ─────┘
```

This shared access is where we need to be careful.

The part of the program that accesses `balance` is called the **critical section**.

---

# 2. What Is a Critical Section?

A critical section is simply:

> **The portion of code that accesses shared data/resources and must be protected from unsafe concurrent access.**

For example:

```c
balance = balance + 10;
```

If `balance` is shared between threads, this operation may be part of a critical section.

Conceptually:

```text
┌─────────────────────────────┐
│        Program              │
│                             │
│   Normal Code               │
│        ↓                    │
│   Critical Section          │
│        ↓                    │
│   Normal Code               │
│                             │
└─────────────────────────────┘
```

The critical section is **not necessarily the entire program**.

It is only the part that needs controlled access to shared resources.

---

# 3. Why Do We Need a Critical Section?

Because multiple processes/threads can execute concurrently.

Imagine:

```text
counter = 0
```

Two threads execute:

```c
counter++;
```

We expect:

```text
0 + 1 + 1 = 2
```

But `counter++` can conceptually involve:

```text
READ counter
     ↓
ADD 1
     ↓
WRITE counter
```

Possible execution:

```text
Thread 1              Thread 2
   │                     │
 Read 0                  │
   │                     │
   │                  Read 0
   │                     │
 Add 1                   │
   │                  Add 1
   │                     │
 Write 1                 │
   │                  Write 1
   ↓                     ↓

Final counter = 1
```

Expected:

```text
2
```

Actual:

```text
1
```

This is a **race condition**.

Therefore, we need to protect the shared operation.

---

# 4. Critical Section and Race Condition

These two concepts are closely connected.

### Critical Section

The **code** that accesses shared data.

### Race Condition

The **problem** that can happen when concurrent execution accesses shared data without sufficient synchronization.

Think of it like:

```text
Shared Resource
      ↓
Code accessing it
      ↓
Critical Section
      ↓
No proper synchronization
      ↓
Race Condition
```

So:

> **Critical section is the area that needs protection; race condition is one of the problems that protection helps prevent.**

---

# 5. Simple Real-Life Example

Imagine a bathroom with only **one key**.

```text
Person A
   │
   ├── Gets key
   │
   ├── Uses bathroom
   │
   └── Returns key
```

Person B has to wait:

```text
Person B
   │
   └── Waits for key
```

The bathroom is like a **shared resource**.

The time during which Person A is using it is like the **critical section**.

The key is like a **lock/mutex**.

```text
Bathroom       → Shared Resource
Person inside  → Critical Section
Key            → Mutex/Lock
Person waiting → Blocked/Waiting
```

Only one person can safely use the bathroom at a time.

---

# 6. Structure of a Critical Section

A process generally has four conceptual parts:

```text
┌──────────────────────┐
│    Entry Section     │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│   Critical Section   │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│     Exit Section     │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Remainder Section   │
└──────────────────────┘
```

Let's understand each one.

---

# 7. Entry Section

The **entry section** is the part where a process/thread tries to obtain permission to enter the critical section.

For example:

```text
acquireLock();
```

Conceptually:

```text
Thread
   ↓
"Can I enter?"
   ↓
Try to acquire lock
   ↓
Yes → Enter
No  → Wait
```

Example:

```c
lock(mutex);
```

If another thread already owns the mutex:

```text
Thread 1 → Inside critical section
Thread 2 → Waiting
```

---

# 8. Critical Section

This is the important part.

It contains the code that accesses the shared resource.

Example:

```c
lock(mutex);

balance = balance + 10;

unlock(mutex);
```

Here:

```text
lock(mutex)
      ↓
┌─────────────────────┐
│ balance += 10       │ ← Critical Section
└─────────────────────┘
      ↓
unlock(mutex)
```

The shared operation is protected.

---

# 9. Exit Section

After finishing the critical section, the process/thread releases the synchronization mechanism.

For example:

```c
unlock(mutex);
```

Conceptually:

```text
Critical Section
      ↓
Finished
      ↓
Release lock
      ↓
Another waiting thread may enter
```

---

# 10. Remainder Section

The **remainder section** is everything else that does not require access to the shared resource.

For example:

```text
Entry Section
     ↓
Critical Section
     ↓
Exit Section
     ↓
Remainder Section
```

The remainder section might contain:

```text
Calculations
Input processing
Logging
Local variables
Other independent work
```

---

# 11. Complete Example

Consider:

```c
while (true) {

    // Entry Section
    lock(mutex);

    // Critical Section
    balance = balance + 10;

    // Exit Section
    unlock(mutex);

    // Remainder Section
    performOtherWork();
}
```

The flow is:

```text
             Process/Thread
                    │
                    ↓
             Entry Section
                    │
              Acquire Lock
                    │
                    ↓
          ┌─────────────────┐
          │ Critical        │
          │ Section         │
          │                 │
          │ balance += 10   │
          └────────┬────────┘
                   │
                   ↓
              Exit Section
                   │
              Release Lock
                   │
                   ↓
           Remainder Section
                   │
                   ↓
                 Repeat
```

---

# 12. What Is the Critical Section Problem?

The **Critical Section Problem** is the problem of designing a method/protocol that allows multiple processes or threads to safely access shared resources.

Suppose:

```text
P1 ────────┐
           │
           ├──→ Shared Resource
           │
P2 ────────┘
```

We need a way to decide:

```text
Who enters?
Who waits?
When can the next process enter?
```

A correct solution should satisfy certain requirements.

The three most important ones are:

1. **Mutual Exclusion**
2. **Progress**
3. **Bounded Waiting**

These are extremely important for OS placement interviews.

---

# 13. Requirement 1: Mutual Exclusion

## Definition

> **At most one process/thread can be inside the critical section at a time.**

Suppose P1 enters:

```text
P1 → Critical Section
```

P2 wants to enter:

```text
P2 → WAIT
```

It cannot enter until P1 leaves.

```text
              Critical Section

P1 ────────────────┐
                   │
                   ↓
                [ P1 ]
                   │
                   ↓
                 Exit
                   │
                   ↓
P2 ────────────────┘
                Enter
```

### Simple analogy

One bathroom:

```text
Bathroom → One person at a time
```

That's mutual exclusion.

---

# 14. Why Is Mutual Exclusion Important?

Without mutual exclusion:

```text
P1 ──┐
     ├──→ Shared Resource
P2 ──┘
```

Both could modify the resource simultaneously.

This can cause:

```text
Race Condition
Data Corruption
Lost Updates
Inconsistent State
```

With mutual exclusion:

```text
P1 → Resource
     ↓
   Finish
     ↓
P2 → Resource
```

Access is controlled.

---

# 15. Requirement 2: Progress

This is slightly harder but very important.

Suppose nobody is currently inside the critical section:

```text
Critical Section → EMPTY
```

And:

```text
P1 wants to enter
P2 wants to enter
```

The system should not unnecessarily keep both waiting forever.

Someone should be allowed to enter.

```text
P1 ── Wants to enter ──┐
                       ├──→ One should be selected
P2 ── Wants to enter ──┘
```

### Simple definition

> **If the critical section is free and some processes want to enter, the decision about who enters should not be postponed indefinitely.**

---

# 16. Simple Example of Progress

Suppose:

```text
Critical Section = Free

P1 → Waiting to enter
P2 → Waiting to enter
```

The system should select one:

```text
P1 → Enter
P2 → Wait
```

or:

```text
P2 → Enter
P1 → Wait
```

But this should not happen:

```text
P1 → Wait
P2 → Wait

Critical Section → Empty

... wait forever ...
```

That would violate progress.

---

# 17. Requirement 3: Bounded Waiting

Suppose P1 is waiting.

If other processes keep entering repeatedly:

```text
P2 → Enter
P2 → Exit

P3 → Enter
P3 → Exit

P4 → Enter
P4 → Exit

P5 → Enter
P5 → Exit

...
```

and P1 never gets a chance:

```text
P1 → Waiting forever
```

This is **starvation**.

### Bounded Waiting means:

> **There must be a limit on how many times other processes can enter the critical section after a process has requested entry, before that waiting process gets its turn.**

Simple idea:

```text
P1 waiting
   ↓
P2 enters
   ↓
P2 exits
   ↓
P1 eventually gets a turn
```

---

# 18. Starvation

**Starvation** means a process keeps waiting because other processes repeatedly get access to the resource.

Example:

```text
P1 → Waiting

P2 → Enters
P2 → Exits

P3 → Enters
P3 → Exits

P4 → Enters
P4 → Exits

P5 → Enters
P5 → Exits

...
```

P1 never gets access.

```text
P1 → Starved
```

Bounded waiting helps prevent this kind of indefinite postponement.

---

# 19. Mutual Exclusion vs Progress vs Bounded Waiting

Remember them like this:

| Requirement | Simple Meaning |
|---|---|
| **Mutual Exclusion** | Only one enters at a time |
| **Progress** | If it's free, don't unnecessarily keep everyone waiting |
| **Bounded Waiting** | Don't make one process wait forever |

### Memory trick

```text
Mutual Exclusion → "ONE"
Progress          → "SOMEONE"
Bounded Waiting   → "EVENTUALLY"
```

---

# 20. How Do We Protect a Critical Section?

The basic pattern is:

```text
acquireLock();

    // Critical Section

releaseLock();
```

For example:

```c
lock(mutex);

counter++;

unlock(mutex);
```

The idea is:

```text
             Mutex
               │
        ┌──────┴──────┐
        ↓             ↓
      Thread 1      Thread 2
        │             │
      Gets lock      Waits
        │             │
        ↓             │
    Critical          │
    Section           │
        │             │
        ↓             │
   Releases lock      │
                      ↓
                  Gets lock
```

---

# 21. Mutex

**Mutex** stands for:

> **Mutual Exclusion**

A mutex is commonly used to ensure that only one thread at a time enters a protected critical section.

Example:

```c
lock(mutex);

shared_data++;

unlock(mutex);
```

The important pattern is:

```text
Lock
 ↓
Critical Section
 ↓
Unlock
```

---

# 22. Semaphore

A **semaphore** uses a counter to coordinate access.

For example, suppose there are:

```text
3 identical resources
```

We can conceptually have:

```text
Semaphore = 3
```

Each thread obtains one permit before using a resource.

```text
Thread 1 → acquire → Semaphore = 2
Thread 2 → acquire → Semaphore = 1
Thread 3 → acquire → Semaphore = 0
Thread 4 → waits
```

When one finishes:

```text
Thread 1 → release → Semaphore = 1
```

Now another waiting thread may proceed.

A **binary semaphore** has two logical states and can sometimes be used for mutual exclusion, but it is not identical to a mutex because their semantics and ownership rules differ.

---

# 23. Atomic Operations

Sometimes a critical operation can be performed using an atomic instruction.

For example:

```text
atomic_increment(counter)
```

Instead of:

```text
READ
 ↓
MODIFY
 ↓
WRITE
```

the operation is performed atomically with respect to the relevant synchronization mechanism.

This can avoid a race for that specific operation.

---

# 24. Monitor

A **monitor** is a higher-level synchronization construct that combines:

```text
Shared Data
+
Operations on the Data
+
Synchronization
```

Conceptually:

```text
              Monitor
       ┌──────────────────┐
       │                  │
       │  Shared Data     │
       │                  │
       │  Operations      │
       │                  │
       │  Synchronization │
       │                  │
       └──────────────────┘
```

Only controlled access to the monitor's protected operations is allowed.

Java's `synchronized` mechanism is an example of monitor-style synchronization.

---

# 25. Real-World Example 1: Bank Account

Suppose:

```text
Balance = ₹1,000
```

Two transactions happen:

```text
Transaction A → Withdraw ₹700
Transaction B → Withdraw ₹700
```

Both transactions need to:

```text
1. Read balance
2. Check whether enough money exists
3. Update balance
```

This is a critical operation.

Conceptually:

```text
lock(account);

checkBalance();
withdrawMoney();

unlock(account);
```

Without proper synchronization/transaction control, both operations could observe the same old balance and produce an invalid result.

---

# 26. Real-World Example 2: Ticket Booking

Suppose:

```text
Available seats = 1
```

Two users try to book it.

```text
User A → Check seat
User B → Check seat
```

If both see:

```text
Seat available
```

before either reservation is committed, both may try to reserve it.

The critical operation is effectively:

```text
Check availability
       +
Reserve seat
```

These operations need appropriate concurrency control.

Conceptually:

```text
lock(seat);

if (seat_available)
    reserve();

unlock(seat);
```

In real ticketing systems, databases often use transactions, row-level locks, optimistic concurrency control, or other mechanisms rather than a simple application mutex.

---

# 27. Real-World Example 3: Printer Queue

Suppose multiple users send documents to a printer.

```text
User A ──┐
User B ──┼──→ Printer Queue
User C ──┘
```

If multiple processes modify the queue at the same time without coordination, queue state can become inconsistent.

The operation:

```text
Add job to shared queue
```

may need synchronization.

---

# 28. Real-World Example 4: Shared File

Suppose two processes modify the same file:

```text
Process A ──┐
            ├──→ data.txt
Process B ──┘
```

If both write simultaneously without appropriate coordination, their writes may interfere.

The shared file operation may therefore require:

```text
Lock
 ↓
Write
 ↓
Unlock
```

The exact mechanism depends on the operating system and application.

---

# 29. Critical Section vs Mutex

These are often confused.

### Critical Section

The **code/region** that accesses shared data.

### Mutex

The **synchronization mechanism** used to protect that code.

Think:

```text
Critical Section → Room
Mutex             → Key to the room
```

Example:

```c
lock(mutex);          // Key

counter++;            // Critical Section

unlock(mutex);        // Return key
```

---

# 30. Critical Section vs Race Condition

Another common interview question.

| Critical Section | Race Condition |
|---|---|
| Part of a program | Concurrency bug/problem |
| Accesses shared resource | Result depends on timing/order |
| Needs protection | Can result from insufficient protection |
| Example: `counter++` | Example: final counter becomes 1 instead of 2 |

Remember:

```text
Critical Section → WHERE the shared resource is accessed

Race Condition → WHAT can go wrong
```

---

# 31. Critical Section vs Deadlock

Another important distinction.

### Critical Section

A protected region of code.

### Deadlock

A situation where processes/threads wait indefinitely for resources/events.

Example:

```text
P1 holds Lock A
P1 waits for Lock B

P2 holds Lock B
P2 waits for Lock A
```

```text
P1 ──waits──→ B
↑             │
│             ↓
A ←──waits── P2
```

Neither can continue.

So:

```text
Critical Section → Resource protection concept

Deadlock → Waiting problem
```

---

# 32. Critical Section and Context Switching

Suppose:

```text
Thread 1
    ↓
Critical Section
```

If the thread is interrupted at an unsafe point, another thread might run.

This is why synchronization is necessary.

A mutex can ensure:

```text
Thread 1 → Owns lock
Thread 2 → Cannot enter protected section
```

Even if Thread 1 is temporarily preempted, Thread 2 cannot simply enter the same mutex-protected critical section.

This is one reason **"just don't context-switch"** is not a general solution to synchronization.

---

# 33. Can We Disable Interrupts?

In certain kernel-level situations, interrupts can be disabled briefly while manipulating data that must not be interrupted on that CPU.

Conceptually:

```text
Disable Interrupts
       ↓
Critical Operation
       ↓
Enable Interrupts
```

But this is **not a general solution for user programs**.

Also, on a multi-core CPU:

```text
CPU 1 → interrupts disabled
CPU 2 → can still execute
```

So another CPU could still access shared memory.

Modern systems generally use appropriate synchronization primitives instead.

---

# 34. Important Correction: "Only One Thread" Is Not Always the Whole Story

You'll often see:

> "Only one process/thread should execute the critical section at a time."

This is a useful rule for **mutual exclusion**, but technically, the requirement depends on the shared resource and algorithm.

For example, if multiple threads only read shared data, they may safely execute concurrently.

```text
Reader 1 ──┐
Reader 2 ──┼──→ Shared Data
Reader 3 ──┘
```

No writer is modifying the data.

This is why mechanisms such as **read-write locks** exist:

```text
Multiple Readers → Allowed
Writer            → Exclusive
```

So the more precise statement is:

> **When mutual exclusion is required, only one execution context should enter that protected critical section at a time.**

---

# 35. Critical Section Problem — Technical View

Consider a process:

```text
do {

    Entry Section;

    Critical Section;

    Exit Section;

    Remainder Section;

} while (true);
```

The synchronization protocol controls the entry and exit around the critical section.

The goal is to ensure:

### Mutual Exclusion

```text
∀ time:
At most one process is in the critical section.
```

### Progress

If:

```text
No process is in CS
+
Some processes want to enter
```

then selection of the next process cannot be postponed indefinitely by processes that are not interested in entering.

### Bounded Waiting

After a process requests entry, there must be a finite bound on how many times other processes can enter before it is allowed to enter.

---

# 36. Why These Three Matter

Imagine a synchronization solution.

### Case 1: No Mutual Exclusion

```text
P1 → CS
P2 → CS
```

Both are inside.

❌ Race condition may occur.

---

### Case 2: No Progress

```text
CS → Empty

P1 → Wants to enter
P2 → Wants to enter

Nobody enters
```

❌ System unnecessarily waits.

---

### Case 3: No Bounded Waiting

```text
P1 → Waiting

P2 → Enter
P3 → Enter
P4 → Enter
P5 → Enter
...
```

P1 may starve.

❌ Unfair synchronization.

---

# 37. Placement Interview Questions

## Q1. What is a critical section?

**Answer:**

A critical section is a part of a program where shared resources are accessed or modified and therefore require synchronization to prevent incorrect concurrent access.

---

## Q2. What are the four sections of a process around a critical section?

```text
1. Entry Section
2. Critical Section
3. Exit Section
4. Remainder Section
```

---

## Q3. What is the Critical Section Problem?

**Answer:**

It is the problem of designing a synchronization protocol that allows processes/threads to safely access shared resources while satisfying mutual exclusion, progress, and bounded waiting.

---

## Q4. What are the three requirements of a Critical Section solution?

### 1. Mutual Exclusion

Only one process/thread can execute the critical section at a time.

### 2. Progress

If the critical section is free and processes want to enter, the decision about who enters cannot be postponed indefinitely.

### 3. Bounded Waiting

A waiting process should not be bypassed indefinitely by other processes.

---

# 38. Q5. What is Mutual Exclusion?

**Answer:**

Mutual exclusion ensures that at most one process or thread can execute a particular protected critical section at any given time.

---

# 39. Q6. What is Progress?

**Answer:**

If no process is currently inside the critical section and some processes want to enter, the system should eventually select one of the waiting processes instead of postponing the decision indefinitely.

---

# 40. Q7. What is Bounded Waiting?

**Answer:**

Bounded waiting guarantees that after a process requests entry to the critical section, there is a finite limit on how many times other processes can enter before that process gets its turn.

It helps prevent starvation.

---

# 41. Q8. How can we implement a critical section?

Common mechanisms include:

```text
Mutex
Semaphore
Monitor
Atomic Operations
Spinlocks
Read-Write Locks
Other synchronization primitives
```

The appropriate choice depends on the system and workload.

---

# 42. Q9. Is a critical section the same as a mutex?

**No.**

```text
Critical Section → Protected code

Mutex → Synchronization mechanism used to protect it
```

Example:

```c
lock(mutex);       // Synchronization

counter++;         // Critical Section

unlock(mutex);     // Synchronization
```

---

# 43. Q10. Can a critical section contain multiple statements?

**Yes.**

For example:

```c
lock(mutex);

balance = balance - amount;
transaction_count++;
logTransaction();

unlock(mutex);
```

The entire group may be considered the critical section if all those operations need to be performed under the same protection.

---

# 44. Q11. Can multiple readers be inside simultaneously?

**Yes, depending on the synchronization design.**

If the shared data is read-only for those operations, multiple readers may be allowed.

A **read-write lock** can support:

```text
Reader 1 ──┐
Reader 2 ──┼──→ Read shared data
Reader 3 ──┘

Writer → Must have exclusive access
```

---

# 45. Q12. Does a critical section always mean a race condition exists?

**No.**

A critical section is a region that requires synchronization because it accesses shared state.

A race condition occurs when concurrent operations can interfere in a way that makes correctness depend on timing/order.

Properly protecting a critical section is one way to prevent such problems.

---

# 46. A Complete Mental Model

This is the most important diagram to remember:

```text
                    Multiple Threads
                           │
                           ↓
                    Shared Resource
                           │
                           ↓
                    Shared Access
                           │
                           ↓
                  ┌─────────────────┐
                  │ Critical        │
                  │ Section         │
                  └────────┬────────┘
                           │
                    Need Synchronization
                           │
              ┌────────────┼────────────┐
              ↓            ↓            ↓
            Mutex       Semaphore     Atomic
              │            │          Operation
              └────────────┼────────────┘
                           ↓
                    Safe Access
```

---

# 47. Complete Flow of a Critical Section

Remember this sequence:

```text
             PROCESS / THREAD
                    │
                    ↓
             ENTRY SECTION
                    │
                    ↓
             Acquire Lock
                    │
                    ↓
          ┌──────────────────┐
          │                  │
          │ CRITICAL SECTION │
          │                  │
          │ Shared Resource  │
          │ Access/Update    │
          │                  │
          └────────┬─────────┘
                   │
                   ↓
             EXIT SECTION
                   │
                   ↓
             Release Lock
                   │
                   ↓
          REMAINDER SECTION
                   │
                   ↓
                Continue
```

---

# 48. Technical Explanation

> A **critical section** is a region of concurrent code that accesses shared mutable state or a shared resource and must satisfy an appropriate synchronization policy to preserve correctness.

The classic critical-section problem asks for a protocol that controls entry into the critical section and satisfies:

### Mutual Exclusion

At most one process/thread executes the protected critical section at a time when exclusive access is required.

### Progress

If the critical section is unoccupied and processes are requesting entry, selection of the next entrant should not be postponed indefinitely.

### Bounded Waiting

A process that requests entry should have a finite bound on the number of times other processes can enter before it.

A typical implementation is:

```text
acquire(lock)

    // critical section
    modify shared state

release(lock)
```

Synchronization mechanisms such as **mutexes, semaphores, monitors, spinlocks, atomic operations, condition variables, and read-write locks** can be used depending on the concurrency problem.

---

# 49. Critical Section — Placement Cheat Sheet

For quick revision:

```text
┌─────────────────────────────────────────┐
│       CRITICAL SECTION                  │
├─────────────────────────────────────────┤
│ What?                                   │
│ → Code accessing shared resources      │
│                                         │
│ Why?                                    │
│ → Prevent unsafe concurrent access     │
│                                         │
│ Main problem                            │
│ → Race Condition                        │
│                                         │
│ Structure                               │
│ → Entry                                  │
│ → Critical Section                      │
│ → Exit                                   │
│ → Remainder                              │
│                                         │
│ Requirements                            │
│ → Mutual Exclusion                      │
│ → Progress                               │
│ → Bounded Waiting                        │
│                                         │
│ Synchronization                         │
│ → Mutex                                 │
│ → Semaphore                             │
│ → Monitor                               │
│ → Atomic Operations                     │
│ → Spinlock / RW Lock, etc.              │
└─────────────────────────────────────────┘
```

### The three words to remember

```text
MUTUAL EXCLUSION
       ↓
    ONE AT A TIME

PROGRESS
       ↓
 SOMEONE GETS A TURN

BOUNDED WAITING
       ↓
 EVENTUALLY GET YOUR TURN
```

### The most important interview relationship

```text
Shared Resource
      ↓
Critical Section
      ↓
Synchronization
      ↓
Mutual Exclusion
      ↓
Prevent Race Conditions
```

And don't forget the distinction:

```text
Critical Section → WHERE shared data is accessed

Race Condition   → WHAT can go wrong

Mutex/Semaphore  → HOW access can be controlled

Mutual Exclusion → PROPERTY ensuring one-at-a-time access
```

This connection is fundamental for understanding the next OS topics: **Peterson's Solution, Mutex, Semaphores, Monitors, Spinlocks, Deadlock, Starvation, and Classical Synchronization Problems**.