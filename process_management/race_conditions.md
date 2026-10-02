# Race Condition in Operating System

> A **race condition** occurs when multiple processes or threads access shared data concurrently, at least one of them modifies the data, and the final result depends on the order in which they execute.

In simple terms:

> **When multiple threads/processes "race" to change the same data, the result can become incorrect because the OS may switch between them at an unexpected time.**

---

## 📌 Table of Contents

* [1. First, Understand the Problem](#1-first-understand-the-problem)
* [2. What Is a Race Condition?](#2-what-is-a-race-condition)
* [3. Why Is It Called a "Race"?](#3-why-is-it-called-a-race)
* [4. The Most Important Idea](#4-the-most-important-idea)
* [5. What Is a Shared Resource?](#5-what-is-a-shared-resource)
* [6. What Is Concurrency?](#6-what-is-concurrency)
* [7. The Most Important Example: `balance += 10`](#7-the-most-important-example-balance--10)
* [8. How a Race Condition Happens](#8-how-a-race-condition-happens)
* [9. Step-by-Step Race Condition](#9-step-by-step-race-condition)
* [10. Another Very Simple Example: Counter](#10-another-very-simple-example-counter)
* [11. Why Does This Happen?](#11-why-does-this-happen)
* [12. What Is an Atomic Operation?](#12-what-is-an-atomic-operation)
* [13. Critical Section](#13-critical-section)
* [14. Race Condition vs Critical Section](#14-race-condition-vs-critical-section)
* [15. What Causes Race Conditions?](#15-what-causes-race-conditions)
* [16. Effects of Race Conditions](#16-effects-of-race-conditions)
* [17. How Do We Prevent Race Conditions?](#17-how-do-we-prevent-race-conditions)
* [18. Mutex](#18-mutex)
* [19. Semaphore](#19-semaphore)
* [20. Mutex vs Semaphore](#20-mutex-vs-semaphore)
* [21. Monitor](#21-monitor)
* [22. Atomic Operations](#22-atomic-operations)
* [23. Compare-and-Swap (CAS)](#23-compare-and-swap-cas)
* [24. Disabling Interrupts](#24-disabling-interrupts)
* [25. Proper Scheduling Is Not a Real Fix](#25-proper-scheduling-is-not-a-real-fix)
* [26. Race Condition Example With Threads](#26-race-condition-example-with-threads)
* [27. Solving the Counter Problem With a Mutex](#27-solving-the-counter-problem-with-a-mutex)
* [28. Important Concept: Mutual Exclusion](#28-important-concept-mutual-exclusion)
* [29. Race Condition vs Deadlock](#29-race-condition-vs-deadlock)
* [30. Race Condition vs Data Race](#30-race-condition-vs-data-race)
* [31. Race Condition in Real Life](#31-race-condition-in-real-life)
* [32. Race Condition in Ticket Booking](#32-race-condition-in-ticket-booking)
* [33. Race Condition and Critical Section](#33-race-condition-and-critical-section)
* [34. The Critical Section Problem](#34-the-critical-section-problem)
* [35. What Is Bounded Waiting?](#35-what-is-bounded-waiting)
* [36. Race Condition and OS Scheduler](#36-race-condition-and-os-scheduler)
* [37. Placement Interview Questions](#37-placement-interview-questions)
* [38. Placement-Level Summary](#38-placement-level-summary)
* [39. Technical Explanation](#39-technical-explanation)
* [40. The Three Most Important Things for Placements](#40-the-three-most-important-things-for-placements)

---

# 1. First, Understand the Problem

Imagine you have:

```text
balance = ₹100
```

Two people are using the same bank account.

* Person A wants to **add ₹10**
* Person B wants to **subtract ₹10**

If everything happens one after another:

```text
Starting balance = ₹100

A adds ₹10
₹100 + ₹10 = ₹110

B subtracts ₹10
₹110 - ₹10 = ₹100
```

Final balance:

```text
₹100
```

That's correct.

But computers can execute operations in an overlapping manner.

That's where the problem begins.

---

# 2. What Is a Race Condition?

Suppose two processes access:

```text
balance = 100
```

```text
P1 → Add 10
P2 → Subtract 10
```

We expect:

```text
100 + 10 - 10 = 100
```

But the operations can actually happen like this:

```text
P1 → Read 100
P2 → Read 100
P2 → Calculate 100 - 10 = 90
P1 → Calculate 100 + 10 = 110
P2 → Write 90
P1 → Write 110
```

Final result:

```text
110
```

But we expected:

```text
100
```

The problem occurred because **both processes worked using the same old value**.

This is a **race condition**.

---

# 3. Why Is It Called a "Race"?

Because the processes are effectively racing:

```text
        Shared Variable
              │
       ┌──────┴──────┐
       ↓             ↓
      P1             P2
       │             │
       │    Race     │
       └──────┬──────┘
              ↓
         Final Result
```

Whichever process performs its operations at particular points in time can affect the final result.

So:

```text
Execution Order → Final Result
```

If the execution order changes, the result may change.

---

# 4. The Most Important Idea

Remember this:

> **Race condition = Shared data + Concurrent access + At least one modification + No proper synchronization**

For example:

```text
Shared variable
      +
Multiple threads
      +
At least one thread modifies it
      +
No proper synchronization
      ↓
Race condition
```

---

# 5. What Is a Shared Resource?

A **shared resource** is something that can be accessed by multiple processes or threads.

Examples:

### Shared variable

```c
int balance = 100;
```

### Shared file

```text
data.txt
```

### Shared memory

```text
Process A ──┐
            ├──→ Shared Memory
Process B ──┘
```

### Database record

```text
Account balance
```

### Device

```text
Printer
```

So a shared resource can be:

```text
Variable
File
Memory
Database record
Device
Data structure
```

---

# 6. What Is Concurrency?

**Concurrency** means multiple tasks make progress during overlapping periods of time.

For example:

```text
P1: ████    ████
P2:    ████    ████
```

Their execution overlaps.

On a single CPU core, this can happen through rapid context switching:

```text
P1
 ↓
P2
 ↓
P1
 ↓
P2
```

On multiple CPU cores, they may actually execute simultaneously:

```text
CPU Core 1 → P1
CPU Core 2 → P2
```

### Important

Concurrency does **not automatically mean** a race condition exists.

You need:

```text
Concurrency
     +
Shared mutable data
     +
Unsafe access
     ↓
Potential Race Condition
```

---

# 7. The Most Important Example: `balance += 10`

Many beginners think:

```c
balance = balance + 10;
```

is one operation.

Usually, at the machine/concurrency level, it involves multiple steps:

```text
1. Read balance
2. Add 10
3. Write balance
```

So:

```text
balance = balance + 10
```

can be viewed conceptually as:

```text
READ
 ↓
MODIFY
 ↓
WRITE
```

This is called a **Read-Modify-Write** operation.

---

# 8. How a Race Condition Happens

Suppose:

```text
balance = 100
```

P1:

```text
balance = balance + 10
```

P2:

```text
balance = balance - 10
```

Let's break them down.

### P1

```text
READ balance
      ↓
    100
      ↓
ADD 10
      ↓
    110
      ↓
WRITE 110
```

### P2

```text
READ balance
      ↓
    100
      ↓
SUBTRACT 10
      ↓
     90
      ↓
WRITE 90
```

Now imagine the scheduler switches between them.

---

# 9. Step-by-Step Race Condition

Initial value:

```text
balance = 100
```

### Step 1

P1 reads:

```text
P1 → Read balance
       ↓
      100
```

P1 has calculated:

```text
110
```

but hasn't written it yet.

---

### Step 2

The OS switches to P2.

```text
P2 → Read balance
       ↓
      100
```

P2 also sees:

```text
100
```

because P1 hasn't written 110 yet.

---

### Step 3

P2 calculates:

```text
100 - 10 = 90
```

and writes:

```text
balance = 90
```

---

### Step 4

P1 resumes.

P1 already calculated:

```text
100 + 10 = 110
```

So P1 writes:

```text
balance = 110
```

---

### Final Result

```text
balance = 110
```

But logically:

```text
100 + 10 - 10 = 100
```

Expected:

```text
100
```

Actual:

```text
110
```

That's the race condition.

---

# 10. Another Very Simple Example: Counter

Consider:

```c
int counter = 0;
```

Two threads execute:

```c
counter++;
```

You might think:

```text
0 + 1 + 1 = 2
```

So the answer should be:

```text
counter = 2
```

But:

```c
counter++;
```

is conceptually:

```text
READ counter
ADD 1
WRITE counter
```

Now:

```text
Thread 1                 Thread 2
   │                        │
   │ Read 0                 │
   │                        │
   │                        │ Read 0
   │                        │
   │ Add 1                  │
   │                        │ Add 1
   │                        │
   │ Write 1                │
   │                        │
   │                        │ Write 1
```

Final:

```text
counter = 1
```

Expected:

```text
counter = 2
```

One increment has effectively been lost.

This is called a **lost update**.

---

# 11. Why Does This Happen?

Because the operation:

```text
Read → Modify → Write
```

is not necessarily **atomic**.

For example:

```text
counter++
```

may involve:

```text
READ
 ↓
MODIFY
 ↓
WRITE
```

The scheduler can potentially switch threads between these steps.

For example:

```text
Thread 1
   ↓
READ
   ↓
     ← Context Switch
   ↓
Thread 2
   ↓
READ
   ↓
WRITE
   ↓
     ← Context Switch
   ↓
Thread 1
   ↓
WRITE
```

Now the final result can be incorrect.

---

# 12. What Is an Atomic Operation?

An **atomic operation** is an operation that appears indivisible from the perspective of other threads/processes.

Think of it as:

```text
Atomic Operation
      ↓
Cannot observe a partially completed operation
```

For example, conceptually:

```text
Non-Atomic

READ
 ↓
MODIFY
 ↓
WRITE
```

versus:

```text
Atomic

┌───────────────┐
│ READ+MODIFY   │
│ +WRITE        │
└───────────────┘
```

Other threads cannot interfere with the operation in the middle.

---

# 13. Critical Section

A **critical section** is the part of a program where shared data/resource is accessed or modified and therefore requires synchronization.

Example:

```c
balance = balance + 10;
```

If `balance` is shared between threads, this operation belongs to the critical section.

Conceptually:

```text
Program

Normal Code
     ↓
Critical Section
     ↓
Normal Code
```

The goal is often to ensure:

> **Only one thread/process executes the critical section at a time when mutual exclusion is required.**

---

# 14. Race Condition vs Critical Section

These are related but different.

### Critical Section

A portion of code that accesses shared resources and requires controlled access.

### Race Condition

The incorrect behavior that can occur when concurrent execution accesses shared mutable data without sufficient synchronization.

Example:

```text
Shared balance
      ↓
Critical Section
      ↓
No proper protection
      ↓
Race Condition
```

---

# 15. What Causes Race Conditions?

## 15.1 Shared Data

Multiple threads/processes access the same data.

```text
P1 ──┐
     ├──→ balance
P2 ──┘
```

---

## 15.2 Concurrent Execution

The operations overlap in time.

```text
P1: ███████
P2:   ███████
```

---

## 15.3 Non-Atomic Operation

The operation consists of multiple steps.

```text
READ
 ↓
MODIFY
 ↓
WRITE
```

---

## 15.4 Lack of Synchronization

No mechanism prevents unsafe simultaneous access.

For example:

```text
No Mutex
No Semaphore
No Atomic Operation
```

---

## 15.5 Uncontrolled Scheduling/Preemption

The OS can switch between processes/threads at points that expose the problem.

For example:

```text
P1 → Read
     ↓
   Context Switch
     ↓
P2 → Read
```

The scheduler itself is **not the root cause**; the underlying problem is that the program has shared mutable state that isn't properly synchronized.

---

# 16. Effects of Race Conditions

Race conditions can cause serious problems.

## 16.1 Incorrect Data

Example:

```text
Expected → 100
Actual   → 110
```

---

## 16.2 Lost Updates

Two threads update a value, but one update overwrites the other.

```text
Thread 1 → +1
Thread 2 → +1

Expected → +2
Actual   → +1
```

---

## 16.3 Inconsistent State

Suppose:

```text
balance = 100
transaction_count = 10
```

One operation updates the balance while another reads it before the related data is updated.

The program can temporarily observe an inconsistent state.

---

## 16.4 Unpredictable Results

Running the same program multiple times may produce different results:

```text
Run 1 → 100
Run 2 → 110
Run 3 → 90
Run 4 → 100
```

The exact outcomes depend on the execution interleaving.

---

## 16.5 Security Problems

Race conditions can become security vulnerabilities when an attacker can manipulate the timing between checking and using a resource.

A common class is called:

> **TOCTOU — Time-of-Check to Time-of-Use**

Conceptually:

```text
Check permission
      ↓
      ↓
Attacker changes resource
      ↓
Use resource
```

The condition checked earlier may no longer be true when the resource is actually used.

---

# 17. How Do We Prevent Race Conditions?

The basic idea is:

> **Control access to shared resources.**

There are several mechanisms.

---

# 18. Mutex

**Mutex** means **Mutual Exclusion**.

A mutex ensures that only one thread can enter a protected critical section at a time.

Think of a bathroom with one key:

```text
             Mutex
               │
        ┌──────┴──────┐
        ↓             ↓
      Thread 1      Thread 2
        │             │
        │             │
      Gets key       Waits
        │
        ↓
 Critical Section
        │
        ↓
   Releases key
                      │
                      ↓
                 Gets key
```

Example:

```c
lock(mutex);

balance = balance + 10;

unlock(mutex);
```

Now another thread cannot enter the protected section simultaneously.

---

# 19. Semaphore

A **semaphore** is a synchronization mechanism based on a counter.

It can control access to one or more instances of a resource.

A binary semaphore can behave similarly to a lock in some situations, while counting semaphores are useful when multiple identical resources are available.

Example:

Suppose there are:

```text
3 printers
```

A semaphore could start with:

```text
semaphore = 3
```

Each process takes one permit before using a printer:

```text
Process → wait()
         ↓
      Use printer
         ↓
       signal()
```

When all three printers are occupied:

```text
Semaphore = 0
```

another process must wait.

---

# 20. Mutex vs Semaphore

Very common placement question.

| Mutex                                 | Semaphore                                              |
| ------------------------------------- | ------------------------------------------------------ |
| Primarily used for mutual exclusion   | Used for synchronization/resource counting             |
| Usually has ownership semantics       | Generally does not have the same ownership requirement |
| Typically protects a critical section | Can control access to multiple resource instances      |
| Usually one thread owns the mutex     | A semaphore maintains a count                          |

A simple memory trick:

```text
Mutex     → "Who gets the lock?"
Semaphore → "How many permits are available?"
```

---

# 21. Monitor

A **monitor** is a higher-level synchronization construct that combines:

```text
Shared data
+
Operations
+
Synchronization
```

The monitor ensures that only an appropriate number of threads can execute its protected operations at a time, typically one at a time for mutual exclusion.

Conceptually:

```text
             MONITOR
        ┌───────────────┐
        │ Shared Data   │
        │               │
        │ Functions     │
        │               │
        │ Synchronize   │
        └───────┬───────┘
                │
          Controlled Access
```

Languages such as Java provide monitor-style synchronization through constructs such as:

```java
synchronized
```

---

# 22. Atomic Operations

Another solution is to use atomic operations provided by the hardware/language/runtime.

For example:

```text
atomic increment
```

Conceptually:

```text
counter++
```

can be replaced by an atomic increment primitive.

Then:

```text
Thread 1 → Atomic +1
Thread 2 → Atomic +1
```

The operation is performed safely with respect to other atomic operations on the same variable.

---

# 23. Compare-and-Swap (CAS)

A very important concept for placements and concurrent programming is **Compare-and-Swap (CAS)**.

Conceptually:

```text
CAS(address, expected, new_value)
```

It means:

```text
If current value == expected
        ↓
change it to new_value
Otherwise
        ↓
do nothing/fail
```

Example:

```text
Current counter = 10

Expected = 10
New value = 11
```

If the current value is still 10:

```text
10 → 11
```

If another thread already changed it:

```text
Current = 12
Expected = 10

CAS fails
```

This is widely used to build **lock-free** or **non-blocking** data structures and algorithms.

---

# 24. Disabling Interrupts

In some kernel-level contexts, disabling interrupts can prevent the current CPU from being interrupted during a very small critical section.

Conceptually:

```text
Disable interrupts
       ↓
Critical operation
       ↓
Enable interrupts
```

However, this is **not a general-purpose solution for user programs**.

On multiprocessor systems, disabling interrupts on one CPU does not automatically stop another CPU from accessing the same shared memory.

So this technique is mainly relevant to certain kernel/low-level synchronization situations.

---

# 25. Proper Scheduling Is Not a Real Fix

This is an important correction to remember for interviews.

It is tempting to say:

> "We can prevent race conditions by making the scheduler execute processes in a specific order."

But relying on scheduling order is generally **not a correct synchronization strategy**.

Why?

Because scheduling can change.

```text
Today:

P1 → P2 → P1

Tomorrow:

P2 → P1 → P2
```

A correct concurrent program should not depend on a particular timing or scheduling order.

Instead, use synchronization mechanisms such as:

```text
Mutex
Semaphore
Atomic Operations
Monitor
Condition Variables
```

---

# 26. Race Condition Example With Threads

Consider:

```c
int counter = 0;

Thread 1:
    counter++;

Thread 2:
    counter++;
```

Expected:

```text
counter = 2
```

Possible execution:

```text
             Thread 1             Thread 2

                │
             Read 0
                │
                │                 Read 0
                │
             Add 1
                │
                │                 Add 1
                │
             Write 1
                │
                │                 Write 1
                ↓
           counter = 1
```

The second write overwrites the first update.

---

# 27. Solving the Counter Problem With a Mutex

Conceptually:

```c
lock(mutex);

counter++;

unlock(mutex);
```

Now:

```text
Thread 1
   ↓
Lock
   ↓
counter++
   ↓
Unlock
   ↓
Thread 2
   ↓
Lock
   ↓
counter++
   ↓
Unlock
```

Result:

```text
counter = 2
```

The critical section is protected.

---

# 28. Important Concept: Mutual Exclusion

**Mutual exclusion** means:

> At most one process/thread can execute a particular critical section at a time.

For example:

```text
        Critical Section

Thread 1 ────────┐
                 │
                 ↓
            [ ALLOWED ]
                 ↑
                 │
Thread 2 ────────┘
               WAIT
```

Only one gets access.

---

# 29. Race Condition vs Deadlock

These are often confused in interviews.

### Race Condition

The result depends on the timing/order of concurrent operations.

```text
Timing
  ↓
Different result
```

### Deadlock

Processes/threads wait forever for resources held by each other.

```text
P1 waits for P2
P2 waits for P1
```

So:

```text
Race Condition → Wrong/unpredictable result

Deadlock       → Processes can become permanently blocked
```

They are different problems.

---

# 30. Race Condition vs Data Race

These terms are related but should not be treated as perfectly interchangeable.

A **data race**, in the common programming-language sense, occurs when multiple threads access the same memory location concurrently, at least one access is a write, and the accesses are not properly synchronized according to the language's memory model.

A **race condition** is broader:

> The correctness of the program depends on the timing or ordering of concurrent events.

So:

```text
Data race
   ↓
A specific kind of unsafe concurrent memory access

Race condition
   ↓
Broader timing/order-dependent correctness problem
```

A race condition can involve events other than a simple shared-memory data race.

---

# 31. Race Condition in Real Life

## Bank Account

Suppose:

```text
Balance = ₹1,000
```

Two transactions happen:

```text
Transaction A → Withdraw ₹700
Transaction B → Withdraw ₹700
```

If both transactions independently check:

```text
Balance >= ₹700
```

and both see:

```text
₹1,000
```

they may both proceed.

Without proper transactional synchronization:

```text
₹1,000
   ↓
A sees ₹1,000
B sees ₹1,000
   ↓
Both approve
```

This can result in an invalid state.

This is why financial systems require strong concurrency control and transactional guarantees.

---

# 32. Race Condition in Ticket Booking

Suppose only **one seat** is available:

```text
Seat A1 → Available
```

Two users click "Book" at almost the same time.

```text
User A → Check A1 → Available
User B → Check A1 → Available
```

Both think they can book it.

Without proper synchronization/transaction handling:

```text
User A → Book A1
User B → Book A1
```

Now the system has a consistency problem.

The solution is not simply "run one request first"; the booking operation must be designed with proper concurrency control.

---

# 33. Race Condition and Critical Section

A useful relationship to remember:

```text
Shared Resource
      ↓
Critical Section
      ↓
Needs Synchronization
      ↓
If not synchronized properly
      ↓
Race Condition
```

Example:

```c
// Shared variable
int counter = 0;

// Critical section
counter++;
```

Protection:

```c
lock(mutex);

counter++;

unlock(mutex);
```

---

# 34. The Critical Section Problem

In Operating Systems, the **Critical Section Problem** is about designing a protocol that allows processes/threads to safely access shared resources.

A correct solution is commonly expected to satisfy:

### 1. Mutual Exclusion

Only one process/thread can be inside the critical section at a time.

```text
P1 → Critical Section
P2 → Must wait
```

### 2. Progress

If no process is inside the critical section and some processes want to enter, the decision about who enters should not be postponed indefinitely.

### 3. Bounded Waiting

A process should not wait forever while other processes repeatedly enter the critical section.

These three properties are **very important for OS placements**.

---

# 35. What Is Bounded Waiting?

Suppose:

```text
P1 wants to enter
```

but:

```text
P2 → enters
P3 → enters
P4 → enters
P5 → enters
...
```

and P1 keeps waiting forever.

That's **starvation**.

A good synchronization algorithm should provide a bound on how long a waiting process can be bypassed.

Conceptually:

```text
P1 waiting
   ↓
P2 enters
   ↓
P3 enters
   ↓
P1 eventually gets turn
```

---

# 36. Race Condition and OS Scheduler

The scheduler can make race conditions easier to observe because it may interrupt a thread/process between the steps of a non-atomic operation.

For example:

```text
P1:
READ balance
      ↓
  Context Switch
      ↓
P2:
READ balance
WRITE balance
      ↓
Context Switch
      ↓
P1:
WRITE balance
```

But remember:

> **The scheduler does not create the underlying bug. The unsafe shared-state access does.**

---

# 37. Placement Interview Questions

## Q1. What is a race condition?

**Answer:**

A race condition occurs when multiple processes or threads access shared mutable data concurrently and the result depends on their execution order or timing. It can produce incorrect or unpredictable results when proper synchronization is missing.

---

## Q2. Give an example of a race condition.

Suppose:

```text
counter = 0
```

Two threads execute:

```text
counter++
```

If both read `0` before either writes the updated value, both may write `1`.

Therefore:

```text
Expected → 2
Actual   → 1
```

This is a race condition.

---

## Q3. Why is `counter++` not necessarily atomic?

Because it conceptually consists of:

```text
Read
 ↓
Modify
 ↓
Write
```

Another thread may execute between these steps.

---

## Q4. How can you prevent a race condition?

Common methods include:

```text
Mutex
Semaphore
Monitor
Atomic operations
Appropriate locks/synchronization primitives
```

The choice depends on the problem and execution environment.

---

## Q5. What is a critical section?

A critical section is the portion of code that accesses shared data or resources and therefore requires controlled/synchronized access.

---

## Q6. What is mutual exclusion?

Mutual exclusion ensures that only one thread/process can execute a protected critical section at a time.

---

## Q7. What is the difference between mutex and semaphore?

**Mutex** is primarily used for mutual exclusion and generally has ownership semantics.

**Semaphore** maintains a counter and is useful for signaling and controlling access to a limited number of resources.

---

## Q8. Can a single-core CPU have race conditions?

**Yes.**

This is a very important interview question.

Race conditions do not require two CPU cores.

A single CPU can switch between threads:

```text
Thread 1
   ↓
Context Switch
   ↓
Thread 2
   ↓
Context Switch
   ↓
Thread 1
```

If the switch occurs between the steps of a non-atomic operation, a race condition can occur.

---

## Q9. Does concurrency always cause a race condition?

**No.**

Concurrency becomes problematic when concurrent execution interacts with shared mutable state without sufficient synchronization.

For example:

```text
Thread 1 → Read-only data
Thread 2 → Read-only data
```

There may be concurrency but no race condition caused by those accesses.

---

## Q10. Is a race condition the same as deadlock?

**No.**

```text
Race Condition
→ Result depends on execution timing/order.

Deadlock
→ Processes/threads wait indefinitely for resources/events.
```

---

# 38. Placement-Level Summary

Remember this chain:

```text
Multiple Threads/Processes
          ↓
      Shared Data
          ↓
    Concurrent Access
          ↓
   Read/Modify/Write
          ↓
   No Synchronization
          ↓
    Race Condition
          ↓
Incorrect / Unpredictable Result
```

To prevent it:

```text
                Synchronization
                       │
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
     Mutex          Semaphore       Atomic Operation
       │               │                │
       └───────────────┼────────────────┘
                       ↓
              Safe Shared Access
```

---

# 39. Technical Explanation

Now the technical definition:

> A **race condition** is a concurrency-related correctness problem in which the behavior or final state of a program depends on the relative timing or ordering of concurrent operations. When multiple execution contexts access shared mutable state without sufficient synchronization, non-atomic read-modify-write operations can interleave and produce inconsistent results.

For example:

```text
Initial:
counter = 0

T1: Read counter → 0
T2: Read counter → 0
T1: Write 1
T2: Write 1

Final:
counter = 1

Expected:
counter = 2
```

The fundamental problem is the **unsafe interleaving** of operations.

A correct synchronization mechanism establishes the necessary ordering and/or mutual exclusion so that the shared state remains consistent according to the program's concurrency requirements.

---

# 40. The Three Most Important Things for Placements

If you have very little time, remember these:

### 1. Race Condition

```text
Concurrent access to shared mutable data
+
Unsafe synchronization
=
Race condition
```

### 2. Critical Section

```text
Part of code accessing shared resource
```

### 3. Prevention

```text
Mutex
Semaphore
Atomic Operations
Monitor
Other appropriate synchronization mechanisms
```

And remember the classic example:

```text
counter = 0

T1: counter++
T2: counter++

Expected → 2
Possible → 1

Why?
READ → MODIFY → WRITE
can interleave.
```

> **Interview one-liner:**
> A race condition occurs when the correctness of a concurrent program depends on the timing or ordering of accesses to shared mutable data, causing different or incorrect results when operations interleave unexpectedly.
