# Hardware-Based Solutions in Process Synchronization

> Hardware-based synchronization uses special atomic CPU instructions to ensure that only the appropriate process or thread accesses a shared resource at a time.

## Table of Contents

1. [The Basic Problem](#1-the-basic-problem)
2. [What Does Atomic Mean?](#2-what-does-atomic-mean)
3. [What Are Hardware-Based Solutions?](#3-what-are-hardware-based-solutions)
4. [Test-and-Set (TAS)](#4-test-and-set-tas)
5. [Swap Instruction](#5-swap-instruction)
6. [Compare-and-Swap (CAS)](#6-compare-and-swap-cas)
7. [Spinlock](#7-spinlock)
8. [Busy Waiting](#8-busy-waiting)
9. [Mutual Exclusion, Progress, and Bounded Waiting](#9-mutual-exclusion-progress-and-bounded-waiting)
10. [Comparison of All Methods](#10-comparison-of-all-methods)
11. [Real-Life Examples](#11-real-life-examples)
12. [Technical Explanation](#12-technical-explanation)
13. [Common Mistakes and Important Details](#13-common-mistakes-and-important-details)
14. [Placement Interview Questions](#14-placement-interview-questions)
15. [Quick Revision Cheat Sheet](#15-quick-revision-cheat-sheet)

---

## 1. The Basic Problem

### Imagine a shared bank account

Suppose two people, A and B, are using the same bank account, which contains ₹1,000.

Both withdraw ₹100 at almost the same time.

The account balance is stored in shared memory.

A simplified withdrawal operation looks like this:

```text
balance = read_balance()
balance = balance - 100
write_balance(balance)
```

The problem is that reading, calculating, and writing may be separate operations.

Imagine this execution:

| Step | Person A | Person B | Balance in memory |
|---|---|---|---:|
| 1 | Reads ₹1,000 | | ₹1,000 |
| 2 | | Reads ₹1,000 | ₹1,000 |
| 3 | Calculates ₹900 | | ₹1,000 |
| 4 | | Calculates ₹900 | ₹1,000 |
| 5 | Writes ₹900 | | ₹900 |
| 6 | | Writes ₹900 | ₹900 |

The correct balance should be ₹800, but the stored balance becomes ₹900.

One withdrawal has effectively been lost!

This is a **race condition**: the result depends on the timing and order of concurrent operations.

### How can the operating system prevent this?

It needs a way to ensure that only one thread at a time performs the protected operation.

We can use a lock.

Think of a shared room with one key:

- If the room is free, a person takes the key and enters.
- If the room is occupied, other people must wait.
- When the person leaves, they return the key.

In operating systems, the protected code is called the **critical section**, and the mechanism controlling entry is called a **lock**.

### The difficulty

What happens if two threads try to take the key at exactly the same time?

If both independently check that the key is available, both might believe they can enter.

We need an operation that checks and claims the lock as one indivisible action.

That is where hardware-based synchronization comes in.

---

## 2. What Does Atomic Mean?

**Atomic means an operation happens as one indivisible unit from the perspective of other threads.**

Imagine a special machine that gives away a single ticket.

Two people press the button simultaneously. The machine must guarantee that only one person gets the available ticket.

The machine doesn't let both people separately check availability before claiming the ticket.

An atomic CPU instruction provides a similar guarantee for a memory operation.

For example, an atomic operation can:

1. Read the current value of a lock.
2. Change its value.
3. Return the old value.

Other threads cannot interleave their operations inside that atomic operation.

### Ordinary operation versus atomic operation

| Ordinary operations | Atomic operation |
|---|---|
| Reading and writing can be separate steps. | The specified operation is indivisible. |
| Two threads can interfere between steps. | Other threads cannot interleave inside the atomic operation. |
| May cause race conditions. | Can be used to build safe synchronization mechanisms. |

**Important:** Atomic does not mean the entire program executes without interruption. It means that the particular atomic operation cannot be observed as partially completed.

---

## 3. What Are Hardware-Based Solutions?

Hardware-based solutions use CPU-supported atomic instructions to coordinate access to shared memory.

The CPU provides operations that software can use to build locks and other synchronization mechanisms.

The main methods in this topic are:

- **Test-and-Set (TAS):** Read a value and set it to `true` atomically.
- **Swap:** Atomically exchange two values.
- **Compare-and-Swap (CAS):** Change a value only if it matches an expected value.
- **Spinlock:** A lock mechanism commonly built using TAS or CAS.

A useful distinction:

TAS, Swap, and CAS are atomic operations or instruction families. A spinlock is a synchronization mechanism that can be built using those operations.

---

## 4. Test-and-Set (TAS)

### 4.1 Understand the idea first

Imagine a room with a sign:

- `false` = room is free.
- `true` = room is occupied.

A person needs to do two things:

1. Check whether the room is free.
2. Mark it as occupied.

If these are separate operations, two people might both enter.

Test-and-Set solves this by combining the check and update into one atomic operation.

### 4.2 How Test-and-Set works

Assume the shared lock is initially:

```text
lock = false
```

The operation behaves like this:

```text
TestAndSet(lock):
    old_value = lock
    lock = true
    return old_value
```

This is **conceptual pseudocode**. The entire operation must be atomic; implementing these three lines as ordinary, independently executed reads and writes would not provide the required guarantee.

Notice the important detail: it returns the **old value**, not the new value.

| Lock before operation | Lock after operation | Returned value | Meaning |
|---|---|---|---|
| `false` | `true` | `false` | The caller claimed the free lock. |
| `true` | `true` | `true` | The lock was already occupied. |

### 4.3 Pseudocode

```c
// Shared variable
bool lock = false;

while (true) {

    // Entry section
    while (TestAndSet(&lock)) {
        // Keep waiting
    }

    // Critical section
    // Access the shared resource safely

    // Exit section
    lock = false;

    // Remainder section
    // Perform other work
}
```

This illustrates the algorithm; real code must use an appropriate atomic operation and correct memory-ordering semantics.

### 4.4 Walk through an example

Suppose Thread A and Thread B want the lock.

Initially:

```text
lock = false
```

**Step 1: Thread A executes Test-and-Set.**

- It reads the old value: `false`.
- It changes the lock to `true`.
- It receives `false`.

The condition `while (TestAndSet(&lock))` is false, so A enters the critical section.

**Step 2: Thread B executes Test-and-Set.**

- It reads the old value: `true`.
- The lock remains `true`.
- It receives `true`.

The condition is true, so B keeps waiting.

**Step 3: Thread A finishes.**

A releases the lock:

```c
lock = false;
```

**Step 4: A waiting thread tries again.**

Eventually, one contender executes Test-and-Set while the lock is free and acquires it.

```text
Thread A: acquire → critical section → release
Thread B:          wait → acquire → critical section
```

The exact order depends on scheduling and contention.

### 4.5 Why does it provide mutual exclusion?

Because Test-and-Set is atomic.

When the lock is free, only one operation can successfully observe the old value as `false` and claim the lock. Every competing operation observes the lock as occupied until it is released.

Therefore, assuming correct atomic and memory-ordering behavior, no two threads can hold this lock simultaneously.

### 4.6 Advantages

- Simple concept.
- Uses CPU-supported atomic operations.
- Can provide mutual exclusion.
- Useful for implementing low-level locks.

### 4.7 Disadvantages

- Uses busy waiting.
- Can waste CPU time while the lock is held.
- Does not guarantee fairness.
- A thread may wait indefinitely if other threads repeatedly acquire the lock first.

**Remember:** Test-and-Set provides a basic way to acquire a lock, but it does not automatically provide fair access.

---

## 5. Swap Instruction

### 5.1 Understand the idea

Imagine you have a card marked `true`, meaning, "I want to enter."

The shared lock has a card marked either `false` (free) or `true` (occupied).

The Swap instruction exchanges the values of the two cards atomically.

The local variable is often called `key`.

- `lock` is shared between threads.
- `key` is local to each thread.

### 5.2 How Swap works

Conceptually:

```text
Swap(a, b):
    temp = a
    a = b
    b = temp
```

The hardware-supported swap of the relevant values must happen atomically.

Assume:

```text
lock = false
key = true
```

After `Swap(lock, key)`:

```text
lock = true
key = false
```

The thread has successfully claimed the lock because its `key` is now `false`.

### 5.3 Pseudocode

```c
// Shared variable
bool lock = false;

while (true) {

    // Entry section
    bool key = true;

    while (key) {
        Swap(&lock, &key);
    }

    // Critical section
    // Access shared resource

    // Exit section
    lock = false;

    // Remainder section
    // Perform other work
}
```

Here, `Swap` is a conceptual atomic operation.

### 5.4 Step-by-step example

Initially:

```text
lock = false
key_A = true
key_B = true
```

**Thread A swaps first:**

Before:

```text
lock = false
key_A = true
```

After:

```text
lock = true
key_A = false
```

A exits its waiting loop and enters the critical section.

**Thread B tries to swap:**

Before:

```text
lock = true
key_B = true
```

After:

```text
lock = true
key_B = true
```

B's key remains true, so it continues spinning.

**Thread A releases the lock:**

```text
lock = false
```

B continues trying. When B successfully swaps while the lock is free, its key becomes false, and it can enter.

### 5.5 Why does Swap provide mutual exclusion?

Each thread can enter only after its own `key` becomes false.

That happens when its atomic Swap operation takes a free lock and changes the shared lock to occupied.

Only one contender can perform that successful transition from free to occupied at a time.

### 5.6 Advantages and disadvantages

Advantages:

- Simple lock construction.
- Can provide mutual exclusion.
- Uses atomic hardware support.

Disadvantages:

- Busy waiting wastes CPU cycles.
- No guaranteed bounded waiting in the basic algorithm.
- Threads may repeatedly compete for the lock.

**Key idea:** Test-and-Set atomically sets the shared value and returns its old value. Swap atomically exchanges a shared value with a thread's local value.

---

## 6. Compare-and-Swap (CAS)

### 6.1 Understand the idea

CAS stands for **Compare-and-Swap**, sometimes called Compare-and-Set.

Imagine a door with a lock value of `0` when free and `1` when occupied.

A person says:

> "If the lock is still `0`, change it to `1`. Otherwise, leave it alone."

CAS performs the comparison and conditional update atomically.

This is different from blindly setting the lock to `1`, because it changes the value only if the expected value matches.

### 6.2 How CAS works

Conceptually, CAS takes three arguments:

```text
CAS(target, expected, new_value)
```

It:

1. Reads the current value.
2. Compares it with `expected`.
3. If they match, stores `new_value`.
4. Reports whether the comparison succeeded.

The comparison and any resulting update must be one atomic operation.

For example:

```text
CAS(lock, 0, 1)
```

means:

"If `lock` is currently `0`, change it to `1`."

| Current lock | Expected value | New value | Result |
|---|---:|---:|---|
| `0` | `0` | `1` | Success; lock becomes `1`. |
| `1` | `0` | `1` | Failure; lock remains `1`. |

### 6.3 Pseudocode

```c
// Shared lock: 0 = free, 1 = occupied
atomic_int lock = 0;

while (true) {

    // Entry section
    while (!CAS(&lock, 0, 1)) {
        // Keep trying while acquisition fails
    }

    // Critical section
    // Access shared resource

    // Exit section
    atomic_store_release(&lock, 0);

    // Remainder section
    // Perform other work
}
```

The names `CAS` and `atomic_store_release` represent suitable atomic operations with appropriate memory-ordering semantics. Exact APIs vary by programming language and platform.

### 6.4 Walk through an example

Initially:

```text
lock = 0
```

Thread A executes:

```text
CAS(lock, 0, 1)
```

Since the current value matches the expected value, CAS changes the lock to `1` and succeeds.

Thread B then executes the same operation.

The current lock is `1`, not `0`, so CAS fails and B continues waiting.

When A releases the lock, it stores `0`. A waiting thread can then retry.

### 6.5 How is CAS different from Test-and-Set?

| Test-and-Set | Compare-and-Swap |
|---|---|
| Sets the target to a specified busy value, typically `true`. | Updates the target only if its current value matches an expected value. |
| Returns the old value in the usual form. | Commonly reports success or returns the observed old value. |
| Useful for simple lock acquisition. | Useful for locks, counters, stacks, queues, and other atomic updates. |

### 6.6 Is CAS always more efficient?

Not necessarily.

CAS is more flexible because it can conditionally update values, but its performance depends on the hardware, implementation, and level of contention.

When many threads repeatedly compete for one lock, CAS can still lead to heavy spinning and cache traffic.

### 6.7 Does CAS mean lock-free?

No. This is an important placement distinction.

CAS is an atomic primitive that can be used to build **lock-free algorithms**, but a spinlock built using CAS is still a lock and may block progress for other threads.

A lock-free algorithm has a formal progress guarantee: the system as a whole continues making progress even if individual threads are delayed.

**Remember:** CAS is a building block, not a guarantee that the complete program is lock-free.

---

## 7. Spinlock

### 7.1 What is a spinlock?

A spinlock is a lock in which a thread repeatedly tries to acquire the lock instead of going to sleep while waiting.

It is commonly implemented using atomic instructions such as Test-and-Set or CAS.

Imagine waiting outside a room and continuously checking whether the door has opened.

You do not sit down or sleep. You keep checking.

That is spinning.

### 7.2 Pseudocode

```c
atomic_int lock = 0;

void acquire() {
    while (!CAS(&lock, 0, 1)) {
        // Busy wait
    }
}

void release() {
    atomic_store_release(&lock, 0);
}

while (true) {

    acquire();

    // Critical section

    release();

    // Remainder section
}
```

This is illustrative pseudocode. A production implementation must use appropriate atomic acquire/release operations.

### 7.3 How it works

1. A thread calls `acquire()`.
2. If the lock is free, CAS changes `0` to `1`.
3. The thread enters the critical section.
4. If the lock is occupied, the thread keeps trying.
5. The owner finishes and calls `release()`.
6. A waiting thread can then acquire the lock.

### 7.4 Why use a spinlock?

Suppose a lock is normally held for only a few CPU instructions.

Putting the waiting thread to sleep and later waking it could cost more than briefly spinning.

A spinlock can be useful when:

- The critical section is extremely short.
- The waiting time is expected to be small.
- Blocking or scheduling overhead would be undesirable.
- The system has appropriate multiprocessor support.

### 7.5 Advantages

- Avoids the sleep/wakeup overhead associated with blocking locks.
- Can be efficient for very short critical sections.
- Useful in certain kernel and multiprocessor situations.

### 7.6 Disadvantages

- Wastes CPU cycles while waiting.
- Performs poorly when locks are held for long periods.
- Can suffer from starvation.
- May perform badly under heavy contention.
- Can be disastrous if a waiting thread prevents the lock owner from running, such as in certain single-CPU scheduling situations.

**Important:** A spinlock is not automatically better than a mutex. The right choice depends on expected waiting time, scheduling, contention, and the operating environment.

---

## 8. Busy Waiting

Busy waiting means repeatedly checking a condition instead of sleeping or blocking.

For example:

```c
while (lock_is_busy()) {
    // Keep checking
}
```

The CPU continues executing the loop while the lock remains unavailable.

### Busy waiting versus blocking

| Busy waiting | Blocking |
|---|---|
| Thread keeps trying to acquire the lock. | Thread waits without continuously running the acquisition loop. |
| Consumes CPU time while waiting. | Can save CPU time during longer waits. |
| May avoid sleep/wakeup overhead. | May incur scheduling and wakeup overhead. |
| Often useful for very short waits. | Often preferable for longer waits. |

A mutex may block a thread when it cannot acquire the lock, although some mutex implementations also use short spinning before blocking.

### A simple example

Suppose A holds the lock for 1 microsecond.

B spins for a short time and acquires the lock immediately after A releases it. Spinning may be efficient.

Now suppose A holds the lock for 2 seconds.

B spinning for the whole 2 seconds wastes a great deal of CPU time. A blocking approach is usually more appropriate.

The exact break-even point depends on the machine and workload.

---

## 9. Mutual Exclusion, Progress, and Bounded Waiting

These three properties are central to the critical-section problem.

### 9.1 Mutual exclusion

At most one thread can be inside a protected critical section at a time when exclusive access is required.

Test-and-Set, Swap, and CAS can provide the basis for this guarantee when used correctly.

### 9.2 Progress

If the critical section is empty and some threads want to enter, the selection of the next thread should not be postponed indefinitely by unrelated threads.

A free lock means a contender can potentially acquire it. However, this does not mean every waiting thread will eventually succeed.

### 9.3 Bounded waiting

After a thread requests entry, there must be a finite bound on how many times other threads can enter before that thread gets its turn.

This prevents indefinite overtaking.

### Do basic hardware spinlocks guarantee all three?

| Property | Basic TAS lock | Basic Swap lock | Basic CAS spinlock |
|---|---|---|---|
| Mutual exclusion | Yes, with correct atomic operations | Yes, with correct atomic operations | Yes, with correct atomic operations |
| Progress for the system | A contender can acquire a free lock, assuming execution continues | A contender can acquire a free lock, assuming execution continues | A contender can acquire a free lock, assuming execution continues |
| Bounded waiting / fairness | Not guaranteed | Not guaranteed | Not guaranteed |

These basic locks do not guarantee that a particular waiting thread will eventually win. Fair locks need additional mechanisms, such as ticket queues or queue-based lock algorithms.

---

## 10. Comparison of All Methods

| Feature | Test-and-Set | Swap | CAS | Spinlock |
|---|---|---|---|---|
| What is it? | Atomic operation | Atomic operation | Atomic conditional update | Locking mechanism |
| Main action | Sets the target and returns its old value | Exchanges two values | Updates only if the expected value matches | Repeatedly attempts acquisition |
| Can provide mutual exclusion? | Yes | Yes | Yes | Yes, when correctly implemented |
| Busy waiting? | In a basic spinlock built with it | In a basic spinlock built with it | In a basic spinlock built with it | Yes, by definition |
| Fairness guaranteed? | No | No | No | Not inherently |
| Common use | Low-level locking | Low-level synchronization | Locks and other atomic algorithms | Short-duration locking |

### The relationship to remember

```text
CPU atomic instructions
    |
    +-- Test-and-Set
    |
    +-- Swap
    |
    +-- Compare-and-Swap (CAS)
              |
              v
      Synchronization mechanisms
              |
              +-- Spinlock
              +-- Other lock-free or lock-based algorithms
```

The diagram shows common relationships, not the only possible implementations.

---

## 11. Real-Life Examples

### Example 1: Ticket booking

Two customers try to reserve the last seat.

Without synchronization, both may see that the seat is available and both may attempt to book it.

A lock can protect the reservation operation:

```text
Acquire lock
    Check seat availability
    Reserve the seat if available
Release lock
```

Only one thread at a time performs the protected operation.

### Example 2: Shared counter

Suppose multiple threads increment a shared counter.

```c
counter = counter + 1;
```

This may involve reading the counter, adding one, and writing the result.

A race condition can cause lost increments.

A suitable lock can protect the read-modify-write sequence. Alternatively, an atomic increment operation may be used when that operation alone provides the required semantics.

### Example 3: Operating-system kernel data

Several CPU cores may access shared kernel data structures.

A carefully chosen spinlock can protect a very short critical section when blocking would be inappropriate or unnecessarily expensive.

However, a kernel must also account for interrupt handling, scheduling, memory ordering, and other platform-specific requirements.

---

## 12. Technical Explanation

### 12.1 Why ordinary reads and writes are not enough

Consider this conceptual implementation:

```c
bool old = lock;
lock = true;
return old;
```

If two threads execute these ordinary statements concurrently, both may read `false` before either writes `true`.

Both could then enter the critical section.

The solution requires an atomic read-modify-write operation or an equivalent correctly implemented synchronization primitive.

### 12.2 Atomicity versus memory ordering

Atomicity and memory ordering solve related but different problems.

**Atomicity** ensures that the lock acquisition operation itself cannot be interleaved in a way that lets multiple threads claim the same lock.

**Memory ordering** ensures that operations on protected data are properly ordered and visible across threads.

A correctly designed lock normally uses:

- An **acquire** operation when obtaining the lock.
- A **release** operation when releasing it.

Acquire/release synchronization helps ensure that writes made by one lock holder become visible to a later holder.

A hardware atomic instruction by itself does not necessarily provide every memory-ordering guarantee required by a program. The language's atomic API and the platform's memory model matter.

### 12.3 Why `volatile` is not a replacement for atomics

In languages such as C and C++, marking a variable `volatile` does not make a read-modify-write operation atomic and does not, by itself, establish the synchronization required for thread safety.

Use the language's atomic facilities or a suitable synchronization primitive.

### 12.4 Why fairness is separate from atomicity

Atomic instructions ensure that the lock cannot be claimed simultaneously by multiple threads.

They do not necessarily determine which waiting thread gets it next.

A thread that repeatedly loses the competition can starve even though the lock continues to be acquired and released.

Fairness requires additional policy or algorithmic support.

### 12.5 When should you use each approach?

- **Test-and-Set:** Learn the fundamental idea of an atomic lock.
- **Swap:** Understand another hardware-supported way to claim a lock.
- **CAS:** Learn conditional atomic updates and the foundation of many concurrent algorithms.
- **Spinlock:** Consider it when waits are expected to be short and the environment makes spinning appropriate.
- **Blocking mutex:** Often a better choice when a wait might be long or the thread should yield CPU time.

For application development, prefer well-tested synchronization primitives provided by the programming language or operating system instead of implementing low-level locks yourself.

---

## 13. Common Mistakes and Important Details

### Mistake 1: Thinking CAS automatically makes a program lock-free

CAS is a primitive that can help implement lock-free algorithms. A CAS-based spinlock is still a lock.

### Mistake 2: Thinking atomic means the entire program is indivisible

Only the specified atomic operation has that guarantee. Other instructions may execute before or after it, subject to the applicable memory-ordering rules.

### Mistake 3: Assuming mutual exclusion means fairness

A lock can prevent simultaneous entry and still allow one thread to starve.

### Mistake 4: Assuming spinning is always fast

Spinning may be effective for short waits, but it wastes CPU resources during long waits.

### Mistake 5: Releasing the lock incorrectly

The lock must be released only by its current owner, after the protected work is complete. Incorrect release logic can allow concurrent access.

### Mistake 6: Treating the examples as production-ready code

The pseudocode explains the algorithms. Actual implementations require suitable atomic operations, memory ordering, and language-specific rules.

### Mistake 7: Assuming the simplest lock meets every critical-section requirement

Basic TAS, Swap, and CAS spinlocks provide mutual exclusion but do not inherently guarantee bounded waiting.

---

## 14. Placement Interview Questions

### Q1. What are hardware-based solutions to the critical-section problem?

They use special CPU-supported atomic operations, such as Test-and-Set, Swap, and CAS, to coordinate concurrent access to shared resources.

### Q2. What is Test-and-Set?

It is an atomic read-modify-write operation that returns the old value of a target and sets the target to a specified value, commonly `true`.

### Q3. Why must Test-and-Set be atomic?

Without atomicity, two threads might both read the lock as free before either marks it as occupied. Both could enter the critical section.

### Q4. What is the difference between Test-and-Set and Swap?

Test-and-Set sets the target and returns its old value. Swap exchanges the values of two locations.

### Q5. What is Compare-and-Swap?

CAS compares a target's current value with an expected value and updates the target only when they match, as one atomic operation.

### Q6. What is a spinlock?

A spinlock is a lock that makes a waiting thread repeatedly attempt acquisition rather than sleep while waiting.

### Q7. Why can spinlocks be inefficient?

They consume CPU cycles while the lock is unavailable and may perform poorly if the critical section is long or contention is high.

### Q8. Do hardware locks guarantee bounded waiting?

No. Basic TAS-, Swap-, and CAS-based spinlocks do not inherently guarantee fairness or bounded waiting.

### Q9. What is the difference between busy waiting and blocking?

Busy waiting repeatedly checks the condition while consuming CPU time. Blocking suspends the waiting thread so the CPU can run other work.

### Q10. Is CAS always better than Test-and-Set?

No. CAS is more flexible, but performance depends on the hardware, contention, and algorithm.

### Q11. Does `volatile` make a lock thread-safe?

No. `volatile` alone does not provide the atomicity and synchronization required for a correct multithreaded lock in C or C++.

### Q12. What are acquire and release semantics?

Acquire semantics on lock acquisition and release semantics on unlock help ensure that memory operations in the protected section are correctly ordered and visible between lock holders.

### Q13. Can a spinlock starve a thread?

Yes. One thread may repeatedly win the race to acquire the lock while another waits indefinitely.

### Q14. When is a spinlock appropriate?

It can be appropriate when critical sections are very short, the expected wait is small, and the operating environment supports efficient spinning.

### Q15. What is the most important limitation of basic hardware-based locks?

They can provide mutual exclusion, but busy waiting and lack of fairness can make them inefficient or unsuitable for some workloads.

---

## 15. Quick Revision Cheat Sheet

| Term | One-line meaning |
|---|---|
| Atomic operation | An indivisible operation from the perspective of other threads |
| Test-and-Set | Atomically sets a target and returns its old value |
| Swap | Atomically exchanges two values |
| CAS | Atomically updates a value only if it matches an expected value |
| Spinlock | A lock that repeatedly tries to acquire access |
| Busy waiting | Repeatedly checking a condition while consuming CPU time |
| Mutual exclusion | At most one thread enters an exclusively protected section |
| Progress | A waiting system should not be indefinitely prevented from making a choice |
| Bounded waiting | A thread cannot be overtaken indefinitely |
| Starvation | A thread waits indefinitely while others continue to make progress |
| Acquire/release | Memory-ordering semantics used to synchronize access to shared data |

### The 30-second explanation

Hardware-based synchronization uses atomic CPU instructions to prevent multiple threads from claiming the same lock simultaneously.

- **Test-and-Set** atomically sets a lock and returns its old value.
- **Swap** atomically exchanges a shared lock value with a local value.
- **CAS** updates a value only when it matches an expected value.
- **Spinlocks** use atomic operations to repeatedly try to acquire a lock.

These methods can provide mutual exclusion, but basic spinlocks use busy waiting and do not guarantee fairness or bounded waiting. They are useful for some short-duration critical sections, while blocking locks are often better for longer waits.

**Final takeaway:** Atomic instructions provide the building blocks. A correct synchronization mechanism must also account for memory ordering, fairness, scheduling, and the expected duration of contention.