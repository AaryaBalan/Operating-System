# Peterson's Algorithm in Process Synchronization

> **Peterson's Algorithm** is a classic **software-based solution to the critical-section problem for two processes**. It uses two shared variables, `flag[]` and `turn`, to coordinate which process can enter the critical section.

It is mainly important for **Operating Systems exams and placement interviews** because it helps you understand the fundamental ideas behind **mutual exclusion, progress, bounded waiting, busy waiting, and synchronization**.

> **Important practical note:** Peterson's Algorithm is primarily a textbook/theoretical algorithm. Modern systems generally use hardware-supported atomic operations and higher-level synchronization primitives such as mutexes rather than implementing Peterson's algorithm directly.

---

## Table of Contents

- [1. Why Do We Need Peterson's Algorithm?](#1-why-do-we-need-petersons-algorithm)
- [2. The Problem Peterson Solves](#2-the-problem-peterson-solves)
- [3. Simple Example](#3-simple-example)
- [4. Basic Idea](#4-basic-idea)
- [5. The Two Shared Variables](#5-the-two-shared-variables)
  - [5.1 flag](#51-flag)
  - [5.2 turn](#52-turn)
- [6. Peterson's Algorithm](#6-petersons-algorithm)
- [7. Understanding the Algorithm Step by Step](#7-understanding-the-algorithm-step-by-step)
  - [7.1 Step 1: Set flag](#71-step-1-set-flag)
  - [7.2 Step 2: Set turn](#72-step-2-set-turn)
  - [7.3 Step 3: Wait](#73-step-3-wait)
  - [7.4 Step 4: Enter Critical Section](#74-step-4-enter-critical-section)
  - [7.5 Step 5: Leave Critical Section](#75-step-5-leave-critical-section)
- [8. Why Do We Need Both flag and turn?](#8-why-do-we-need-both-flag-and-turn)
- [9. Case 1: Only P0 Wants to Enter](#9-case-1-only-p0-wants-to-enter)
- [10. Case 2: Only P1 Wants to Enter](#10-case-2-only-p1-wants-to-enter)
- [11. Case 3: Both Want to Enter](#11-case-3-both-want-to-enter)
- [12. How Mutual Exclusion Is Guaranteed](#12-how-mutual-exclusion-is-guaranteed)
- [13. Progress](#13-progress)
- [14. Bounded Waiting](#14-bounded-waiting)
- [15. Complete Flow Diagram](#15-complete-flow-diagram)
- [16. Peterson's Algorithm Code](#16-petersons-algorithm-code)
- [17. Code Explanation](#17-code-explanation)
- [18. Important Note About Python Code](#18-important-note-about-python-code)
- [19. Why Peterson's Algorithm Uses Busy Waiting](#19-why-petersons-algorithm-uses-busy-waiting)
- [20. Advantages](#20-advantages)
- [21. Limitations](#21-limitations)
- [22. Peterson's Algorithm vs Mutex](#22-petersons-algorithm-vs-mutex)
- [23. Peterson's Algorithm vs Semaphore](#23-petersons-algorithm-vs-semaphore)
- [24. Real-World Examples](#24-real-world-examples)
- [25. Placement Interview Questions](#25-placement-interview-questions)
- [26. Technical Explanation](#26-technical-explanation)
- [27. Placement Cheat Sheet](#27-placement-cheat-sheet)

---

# 1. Why Do We Need Peterson's Algorithm?

Suppose we have two processes:

```text
P0
P1
```

Both want to access the same shared resource:

```text
             Shared Resource
                    ↑
              ┌─────┴─────┐
              │           │
             P0           P1
```

For example:

```text
P0 → Update shared variable
P1 → Update shared variable
```

If both enter the critical section at the same time, we may get a **race condition**.

So we need a mechanism that decides:

> **Which process can enter the critical section?**

Peterson's Algorithm provides a classic software solution for **exactly two processes**.

---

# 2. The Problem Peterson Solves

Remember the critical-section structure:

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

The problem is controlling the **entry section**.

Peterson's Algorithm tells two processes:

```text
P0 → "I want to enter."
P1 → "I want to enter."
```

and determines who should wait.

---

# 3. Simple Example

Imagine there is **one room with one key**.

Two people:

```text
Person 0
Person 1
```

Both want to enter.

The rules are:

1. Tell everyone you want to enter.
2. If both want to enter, give the other person priority.
3. Wait if the other person wants to enter and it is their turn.
4. Enter when it is safe.
5. When you leave, tell everyone you are no longer interested.

That's essentially the idea behind Peterson's Algorithm.

```text
              ONE ROOM
                 │
          ┌──────┴──────┐
          ↓             ↓
       Person 0       Person 1
          │             │
          └──────┬──────┘
                 ↓
          Decide who enters
                 ↓
          Only one enters
```

The "room" represents the **critical section**.

---

# 4. Basic Idea

Peterson's Algorithm uses two pieces of shared information:

```text
flag[]
turn
```

Think of them as:

```text
flag → "Do I want to enter?"

turn → "If we both want to enter, who gets priority?"
```

So:

```text
flag → Intention
turn → Priority
```

That's the easiest way to remember Peterson's Algorithm.

---

# 5. The Two Shared Variables

## 5.1 `flag`

`flag[i]` tells whether process `Pi` wants to enter the critical section.

For two processes:

```text
flag[0]
flag[1]
```

Suppose:

```text
flag[0] = false
flag[1] = false
```

This means:

```text
P0 → Does not want to enter
P1 → Does not want to enter
```

If:

```text
flag[0] = true
```

it means:

```text
P0 → "I want to enter the critical section."
```

Similarly:

```text
flag[1] = true
```

means:

```text
P1 → "I want to enter."
```

### Remember

```text
flag[i] = true
        ↓
"I want to enter."
```

---

# 5.2 `turn`

`turn` is used when **both processes want to enter**.

For example:

```text
turn = 0
```

means that, under the algorithm's coordination rules, P0 has priority in the conflict.

Similarly:

```text
turn = 1
```

means P1 has priority.

But there is an important detail:

> Each process sets `turn` to the **other** process when it announces its intention to enter.

For P0:

```text
turn = 1
```

For P1:

```text
turn = 0
```

This is what helps break the tie when both want to enter.

---

# 6. Peterson's Algorithm

For process `Pi`, where the other process is `Pj`:

```text
do {

    flag[i] = true;
    turn = j;

    while (flag[j] && turn == j)
        ;   // Wait

    // Critical Section

    flag[i] = false;

    // Remainder Section

} while (true);
```

For P0:

```text
do {

    flag[0] = true;
    turn = 1;

    while (flag[1] && turn == 1)
        ;

    // Critical Section

    flag[0] = false;

    // Remainder Section

} while (true);
```

For P1:

```text
do {

    flag[1] = true;
    turn = 0;

    while (flag[0] && turn == 0)
        ;

    // Critical Section

    flag[1] = false;

    // Remainder Section

} while (true);
```

---

# 7. Understanding the Algorithm Step by Step

Let's understand every line.

---

## 7.1 Step 1: Set `flag`

Suppose P0 wants to enter.

It executes:

```text
flag[0] = true;
```

Meaning:

```text
P0 → "I want to enter the critical section."
```

Similarly, P1 does:

```text
flag[1] = true;
```

Meaning:

```text
P1 → "I want to enter."
```

---

# 7.2 Step 2: Set `turn`

Suppose P0 wants to enter.

P0 executes:

```text
turn = 1;
```

Remember:

```text
P0 → Gives priority to P1
```

Why?

Because if P1 also wants to enter, both cannot enter simultaneously.

P0 is effectively saying:

> "If P1 also wants to enter, I'll give P1 the opportunity."

Similarly, P1 executes:

```text
turn = 0;
```

meaning:

> "If P0 also wants to enter, I'll give P0 the opportunity."

This is the clever part of Peterson's Algorithm.

---

# 7.3 Step 3: Wait

P0 checks:

```text
while (flag[1] && turn == 1);
```

This means:

```text
Is P1 interested?
        AND
Is it P1's turn?
```

If both are true:

```text
flag[1] == true
turn == 1
```

then:

```text
P0 → WAIT
```

Otherwise:

```text
P0 → ENTER
```

---

# 7.4 Step 4: Enter Critical Section

Once the waiting condition becomes false:

```text
while (flag[1] && turn == 1);
```

P0 can enter:

```text
// Critical Section
```

Only one process should reach this point at a time under the algorithm's assumptions.

---

# 7.5 Step 5: Leave Critical Section

When P0 finishes:

```text
flag[0] = false;
```

This means:

```text
P0 → "I'm no longer interested in entering."
```

Now P1 can proceed if it is waiting.

---

# 8. Why Do We Need Both `flag` and `turn`?

This is one of the **most important Peterson's Algorithm interview questions**.

You need both because they solve two different problems.

```text
flag
  ↓
Shows intention

turn
  ↓
Breaks a tie
```

Suppose only `flag` existed.

```text
P0 → flag[0] = true
P1 → flag[1] = true
```

Now:

```text
Both want to enter.
```

Who should enter?

We don't know.

That's why we need:

```text
turn
```

---

## Easy Analogy

Two people want to enter a room.

Both say:

```text
"I want to enter!"
```

That's `flag`.

Then they need a rule:

```text
"If both want to enter, decide whose turn it is."
```

That's `turn`.

So:

```text
flag → Do you want to enter?
turn → Who gets priority if both want to enter?
```

---

# 9. Case 1: Only P0 Wants to Enter

Initial state:

```text
flag[0] = false
flag[1] = false
```

P0 wants to enter.

### Step 1

```text
flag[0] = true
```

Now:

```text
flag[0] = true
flag[1] = false
```

### Step 2

P0:

```text
turn = 1
```

### Step 3

P0 checks:

```text
while (flag[1] && turn == 1)
```

Substitute:

```text
while (false && true)
```

Result:

```text
false
```

So P0 does **not** wait.

```text
P0 → Critical Section
```

Simple.

---

# 10. Case 2: Only P1 Wants to Enter

P1 executes:

```text
flag[1] = true;
turn = 0;
```

P1 checks:

```text
while (flag[0] && turn == 0);
```

Since:

```text
flag[0] = false
```

we get:

```text
false && true
```

which is:

```text
false
```

So P1 enters.

```text
P1 → Critical Section
```

---

# 11. Case 3: Both Want to Enter

This is the most important case.

Suppose both processes want to enter:

```text
P0 → wants to enter
P1 → wants to enter
```

Let's go slowly.

---

## Step 1: P0 Announces

P0:

```text
flag[0] = true;
turn = 1;
```

Meaning:

```text
P0 → "I want to enter."

P0 → "If P1 also wants to enter, give P1 priority."
```

---

## Step 2: P1 Announces

P1:

```text
flag[1] = true;
turn = 0;
```

Meaning:

```text
P1 → "I want to enter."

P1 → "If P0 also wants to enter, give P0 priority."
```

Now:

```text
flag[0] = true
flag[1] = true
turn = 0
```

The last process to write `turn` determines the tie.

Here:

```text
turn = 0
```

---

## Step 3: P0 Checks

P0 checks:

```text
while (flag[1] && turn == 1);
```

Substitute:

```text
while (true && false);
```

because:

```text
turn == 1
```

is false.

Therefore:

```text
P0 → Does NOT wait
P0 → Enters Critical Section
```

---

## Step 4: P1 Checks

P1 checks:

```text
while (flag[0] && turn == 0);
```

Substitute:

```text
while (true && true);
```

Therefore:

```text
P1 → WAIT
```

So:

```text
P0 → Critical Section
P1 → Waiting
```

Only one process enters.

---

# 12. How Mutual Exclusion Is Guaranteed

Let's understand the key logic.

For P0 to enter:

```text
NOT (flag[1] && turn == 1)
```

For P1 to enter:

```text
NOT (flag[0] && turn == 0)
```

Suppose both try to enter simultaneously.

For both to enter:

```text
P0 enters
AND
P1 enters
```

would require conflicting conditions on `turn`.

P0 requires:

```text
turn != 1
```

which means:

```text
turn = 0
```

P1 requires:

```text
turn != 0
```

which means:

```text
turn = 1
```

But `turn` cannot simultaneously be:

```text
0 AND 1
```

Therefore, both cannot pass their waiting conditions simultaneously under the algorithm's assumptions.

Hence:

> **Peterson's Algorithm provides mutual exclusion for two processes.**

---

# 13. Progress

Peterson's Algorithm also satisfies **progress**.

Suppose:

```text
P0 → wants to enter
P1 → does not want to enter
```

Then:

```text
flag[1] = false
```

P0 checks:

```text
while (flag[1] && turn == 1);
```

Since:

```text
flag[1] = false
```

the condition becomes:

```text
false
```

Therefore:

```text
P0 → enters
```

The same logic works for P1.

So if the other process isn't interested in entering, the requesting process doesn't wait unnecessarily.

---

# 14. Bounded Waiting

Suppose:

```text
P0 → wants to enter
P1 → wants to enter
```

Peterson's Algorithm uses `turn` to prevent both from continuously overtaking each other.

If P0 is waiting because P1 has priority, P1 cannot stay in the critical section forever.

Eventually:

```text
P1 → exits
P1 → flag[1] = false
```

Then P0's waiting condition becomes:

```text
flag[1] && turn == 1
```

Since:

```text
flag[1] = false
```

the condition becomes:

```text
false
```

Therefore:

```text
P0 → enters
```

This provides bounded waiting under the algorithm's assumptions.

---

# 15. Complete Flow Diagram

The complete Peterson flow is:

```text
                    P0
                     │
                     ↓
             flag[0] = true
                     │
                     ↓
               turn = 1
                     │
                     ↓
        ┌────────────────────────┐
        │ flag[1] && turn == 1 ? │
        └───────────┬────────────┘
                    │
             ┌──────┴──────┐
             │             │
            YES            NO
             │             │
             ↓             ↓
           WAIT       Critical Section
                           │
                           ↓
                    flag[0] = false
                           │
                           ↓
                    Remainder Section
```

For P1:

```text
                    P1
                     │
                     ↓
             flag[1] = true
                     │
                     ↓
               turn = 0
                     │
                     ↓
        ┌────────────────────────┐
        │ flag[0] && turn == 0 ? │
        └───────────┬────────────┘
                    │
             ┌──────┴──────┐
             │             │
            YES            NO
             │             │
             ↓             ↓
           WAIT       Critical Section
                           │
                           ↓
                    flag[1] = false
                           │
                           ↓
                    Remainder Section
```

---

# 16. Peterson's Algorithm Code

The textbook pseudocode is the most important form to understand:

```c
// Shared variables

bool flag[2] = {false, false};
int turn;

Process P0:

do {
    // Entry Section
    flag[0] = true;
    turn = 1;

    while (flag[1] && turn == 1)
        ;   // Busy wait

    // Critical Section

    // Exit Section
    flag[0] = false;

    // Remainder Section

} while (true);
```

For P1:

```c
Process P1:

do {
    // Entry Section
    flag[1] = true;
    turn = 0;

    while (flag[0] && turn == 0)
        ;   // Busy wait

    // Critical Section

    // Exit Section
    flag[1] = false;

    // Remainder Section

} while (true);
```

---

# 17. Code Explanation

Let's understand the important lines.

## Line 1

```c
flag[0] = true;
```

P0 announces:

```text
"I want to enter."
```

---

## Line 2

```c
turn = 1;
```

P0 says:

```text
"If P1 also wants to enter, give P1 priority."
```

---

## Line 3

```c
while (flag[1] && turn == 1)
```

P0 asks:

```text
Does P1 want to enter?
        AND
Is it P1's turn?
```

If yes:

```text
WAIT
```

If no:

```text
ENTER
```

---

## Critical Section

```c
// Critical Section
```

The process performs the operation on the shared resource.

---

## Exit

```c
flag[0] = false;
```

P0 says:

```text
"I'm finished."
```

This allows P1 to proceed if it is waiting.

---

# 18. Important Note About the Python Code

The Python code in the original article is useful as a **demonstration**, but you should be careful about treating it as a real implementation of Peterson's Algorithm.

The textbook algorithm assumes a shared-memory concurrency model with suitable atomicity and memory-ordering assumptions.

A simplified Python demonstration might look like:

```python
flag = [False, False]
turn = 0

def process(i):
    j = 1 - i

    while True:

        # Entry Section
        flag[i] = True
        turn = j

        while flag[j] and turn == j:
            pass

        # Critical Section
        print(f"Process {i} is in critical section")

        # Exit Section
        flag[i] = False

        # Remainder Section
        print(f"Process {i} is in remainder section")
```

However, **do not use this code as evidence that Python threads are correctly synchronized by Peterson's Algorithm in production**.

Why?

Modern programming languages have their own memory models, runtimes, interpreters, and synchronization mechanisms. Python implementations can also differ.

For placement preparation:

> **Learn the C-style pseudocode and the logic of `flag` + `turn`.**

That's what interviewers usually want when asking about Peterson's Algorithm.

---

# 19. Why Peterson's Algorithm Uses Busy Waiting

Look at:

```c
while (flag[j] && turn == j)
    ;
```

The process keeps checking the condition.

For example:

```text
Check
 ↓
Check
 ↓
Check
 ↓
Check
 ↓
P1 exits
 ↓
Condition becomes false
 ↓
Enter
```

This is called:

> **Busy waiting**

The CPU continues executing the loop instead of blocking the process.

---

# 19.1 Why Is Busy Waiting a Problem?

Suppose P1 has to wait for a long time.

```text
P1
 ↓
while (...)
 ↓
while (...)
 ↓
while (...)
 ↓
while (...)
```

The CPU is being used even though P1 isn't doing useful work.

Therefore:

```text
Peterson's Algorithm
        ↓
Busy Waiting
        ↓
CPU cycles consumed
```

Modern systems generally use blocking synchronization primitives such as mutexes when waiting could be significant.

---

# 20. Advantages

## 20.1 Provides Mutual Exclusion

Only one of the two processes can enter the critical section at a time under the algorithm's assumptions.

---

## 20.2 Provides Progress

If the other process doesn't want to enter, the requesting process can enter.

---

## 20.3 Provides Bounded Waiting

A process cannot be postponed indefinitely by the other process under the algorithm's assumptions.

---

## 20.4 No Special Lock Instruction in the Algorithm

The classic algorithm demonstrates mutual exclusion using shared variables:

```text
flag[]
turn
```

This makes it an important theoretical example.

---

## 20.5 Excellent for Learning Synchronization

It demonstrates:

```text
Intent
  ↓
Priority
  ↓
Waiting
  ↓
Critical Section
  ↓
Exit
```

This makes Peterson's Algorithm extremely useful for understanding the **critical-section problem**.

---

# 21. Limitations

Peterson's Algorithm has several important limitations.

## 21.1 Only Two Processes

Classic Peterson's Algorithm is designed for:

```text
P0
P1
```

not arbitrary numbers of processes.

For multiple processes, other algorithms or synchronization primitives are needed.

---

## 21.2 Busy Waiting

A waiting process continuously checks the condition:

```c
while (...)
    ;
```

This consumes CPU cycles.

---

## 21.3 Mostly Theoretical Today

Modern operating systems generally use:

```text
Hardware Atomic Instructions
        +
Memory Ordering
        +
Mutexes
        +
Semaphores
        +
Other Synchronization Primitives
```

rather than implementing Peterson's Algorithm directly.

---

## 21.4 Modern Memory Models

The classic textbook proof assumes appropriate ordering and visibility of the shared variables.

Modern processors and compilers can reorder memory operations unless the program uses appropriate atomic operations or memory-ordering mechanisms.

Therefore, a naive implementation of:

```c
flag[i] = true;
turn = j;
```

using ordinary non-atomic variables is not sufficient for a production-quality modern concurrent program.

---

## 21.5 Not Suitable for Long Waiting

Because of busy waiting:

```text
Long wait
   ↓
CPU continuously spins
   ↓
CPU resources wasted
```

A blocking mutex is generally more appropriate when waiting may be long.

---

# 22. Peterson's Algorithm vs Mutex

This is a very common placement comparison.

| Peterson's Algorithm | Mutex |
|---|---|
| Software algorithm | Synchronization primitive |
| Designed for 2 processes | Can support many threads |
| Uses `flag` and `turn` | Uses OS/runtime mechanisms |
| Busy waits | Can block/sleep |
| Mainly educational/theoretical | Widely used in real systems |
| Sensitive to memory-model assumptions | Designed with appropriate synchronization semantics |
| No ownership mechanism like a mutex | Usually has ownership semantics |

### Easy memory trick

```text
Peterson
   ↓
Theory + 2 processes + Busy Waiting

Mutex
   ↓
Practical + Many threads + Blocking possible
```

---

# 23. Peterson's Algorithm vs Semaphore

| Peterson's Algorithm | Semaphore |
|---|---|
| Two-process algorithm | General synchronization primitive |
| Uses shared variables | Uses counter/state |
| Busy waits | Can block |
| Mainly theoretical | Widely used in systems |
| Mutual exclusion | Mutual exclusion + signaling/resource counting |

Remember:

```text
Peterson → "Who gets into the critical section?"

Semaphore → "How many permits/resources are available?"
```

---

# 24. Real-World Examples

Peterson's Algorithm itself is generally **not the recommended implementation** for these systems today, but these examples help you understand the synchronization problem it addresses.

---

## 24.1 Shared Counter

```text
counter = 100
```

Two processes:

```text
P0 → counter++
P1 → counter++
```

The critical section is the update.

Peterson's Algorithm can conceptually ensure that only one process performs the update at a time.

---

## 24.2 Shared Printer

Suppose two processes want exclusive access to a printer:

```text
P0 ──┐
     ├──→ Printer
P1 ──┘
```

Only one should control the printer at a time if the operation requires exclusive access.

In a real system, the OS normally uses a **print spooler/queue** rather than Peterson's Algorithm.

---

## 24.3 Shared File

Two processes modify shared data:

```text
P0 ──┐
     ├──→ Shared File
P1 ──┘
```

The operations may require synchronization.

In real systems, file locks or transactional mechanisms are more appropriate.

---

## 24.4 Shared Hardware Resource

Two processes need exclusive access:

```text
P0 ──┐
     ├──→ Device
P1 ──┘
```

Synchronization ensures controlled access.

Again, Peterson's Algorithm is mainly useful here as a conceptual example rather than the preferred modern implementation.

---

# 25. Placement Interview Questions

## Q1. What is Peterson's Algorithm?

**Answer:**

> Peterson's Algorithm is a classic software-based solution to the critical-section problem for two processes. It uses a `flag` array to indicate each process's intention to enter and a `turn` variable to resolve conflicts when both processes want to enter. Under its assumptions, it provides mutual exclusion, progress, and bounded waiting.

---

# Q2. How many processes can Peterson's Algorithm handle?

```text
2 processes
```

Usually:

```text
P0
P1
```

This is one of the easiest questions to ask in an interview.

---

# Q3. What are the shared variables in Peterson's Algorithm?

Two:

```text
flag[]
turn
```

---

# Q4. What does `flag[i]` mean?

```text
flag[i] = true
```

means:

> Process `Pi` wants to enter the critical section.

```text
flag[i] = false
```

means:

> Process `Pi` is not trying to enter.

---

# Q5. What does `turn` do?

`turn` resolves the conflict when both processes want to enter.

Each process gives priority to the other process:

```text
P0 → turn = 1
P1 → turn = 0
```

The final value of `turn` helps determine who waits.

---

# Q6. Why is `turn` necessary if we already have `flag`?

Because `flag` only tells us:

```text
Who wants to enter?
```

It doesn't tell us:

```text
Who should enter when both want to enter?
```

`turn` breaks that tie.

```text
flag → intention
turn → tie-breaking
```

---

# Q7. What is the waiting condition for P0?

```c
while (flag[1] && turn == 1);
```

Meaning:

> P0 waits while P1 also wants to enter and P1 has priority.

---

# Q8. What is the waiting condition for P1?

```c
while (flag[0] && turn == 0);
```

Meaning:

> P1 waits while P0 also wants to enter and P0 has priority.

---

# Q9. What are the three requirements satisfied by Peterson's Algorithm?

```text
1. Mutual Exclusion
2. Progress
3. Bounded Waiting
```

This is extremely important.

---

# Q10. Does Peterson's Algorithm use busy waiting?

**Yes.**

Because it repeatedly checks:

```c
while (...)
    ;
```

---

# Q11. What is the main disadvantage of Peterson's Algorithm?

Important disadvantages include:

```text
Only two processes
Busy waiting
Not a practical general-purpose modern synchronization primitive
Requires appropriate memory-ordering assumptions
```

---

# Q12. Is Peterson's Algorithm used in modern operating systems?

Generally, **not as the primary production synchronization mechanism**.

Modern systems use mechanisms based on:

```text
Atomic CPU instructions
+
Memory-ordering primitives
+
Mutexes
+
Semaphores
+
Spinlocks
+
Condition variables
```

Peterson's Algorithm remains very important for understanding synchronization theory.

---

# Q13. What happens if only P0 wants to enter?

```text
flag[0] = true
flag[1] = false
```

P0's waiting condition:

```text
flag[1] && turn == 1
```

becomes:

```text
false && ...
```

which is false.

Therefore:

```text
P0 → Enters Critical Section
```

---

# Q14. What happens when both processes want to enter?

Both set their flags to `true`.

Then each sets `turn` to the other process.

The process that finds the waiting condition true waits, while the other enters.

Thus:

```text
P0 → Critical Section
P1 → Waiting
```

or:

```text
P1 → Critical Section
P0 → Waiting
```

but not both simultaneously.

---

# Q15. Why does Peterson's Algorithm not need a lock variable?

Because the combination:

```text
flag[]
+
turn
```

acts as the software protocol for controlling entry.

---

# 26. Technical Explanation

Now let's define Peterson's Algorithm at the technical level.

> **Peterson's Algorithm is a classical two-process software mutual-exclusion algorithm for the critical-section problem. It uses shared variables `flag[2]` and `turn` to establish mutual exclusion, progress, and bounded waiting under its assumed shared-memory and memory-ordering model.**

For process `Pi`, with `j` being the other process:

```c
flag[i] = true;
turn = j;

while (flag[j] && turn == j)
    ;
```

Then:

```c
// Critical Section

flag[i] = false;
```

The algorithm works because:

1. `flag[i]` expresses intent to enter.
2. `turn` resolves simultaneous requests.
3. A process waits only when the other process also wants to enter **and** has priority.
4. On leaving, the process resets its flag.
5. The algorithm satisfies the classical critical-section requirements under its assumptions.

---

# 26.1 Mutual Exclusion Proof — Intuition

Suppose both P0 and P1 are inside the critical section.

For P0 to enter:

```text
flag[1] == false
OR
turn != 1
```

If P1 is also inside, then:

```text
flag[1] == true
```

Therefore P0 must have:

```text
turn != 1
```

Since `turn` can be either 0 or 1:

```text
turn = 0
```

Similarly, if P1 is inside while P0 wants to enter:

```text
flag[0] == true
```

P1 must have:

```text
turn != 0
```

Therefore:

```text
turn = 1
```

We cannot have:

```text
turn = 0
```

and:

```text
turn = 1
```

at the same time.

Therefore, both cannot enter simultaneously.

---

# 26.2 Progress

If P0 wants to enter but:

```text
flag[1] = false
```

then:

```text
flag[1] && turn == 1
```

is false.

Therefore P0 enters.

Likewise for P1.

Hence, a process is not prevented from entering when the other process has no interest in the critical section.

---

# 26.3 Bounded Waiting

Suppose P0 is waiting.

P1 cannot remain in the critical section indefinitely.

Eventually:

```text
P1 → flag[1] = false
```

Then P0's condition:

```text
flag[1] && turn == 1
```

becomes false.

P0 can enter.

Thus, under the algorithm's assumptions, P0 cannot be postponed indefinitely by P1.

---

# 27. Placement Cheat Sheet

## Peterson's Algorithm in 30 Seconds

```text
                 PETERSON'S ALGORITHM

                  For 2 processes
                        │
                ┌───────┴───────┐
                ↓               ↓
             flag[]            turn
                │               │
          "I want to        "Who gets
             enter"          priority?"
                │               │
                └───────┬───────┘
                        ↓
                 Waiting Condition
                        │
            ┌───────────┴───────────┐
            ↓                       ↓
          TRUE                    FALSE
            ↓                       ↓
          WAIT                   ENTER CS
                                    │
                                    ↓
                              flag[i] = false
```

---

## Must Remember

```text
Number of Processes
→ 2

Shared Variables
→ flag[]
→ turn

Properties
→ Mutual Exclusion
→ Progress
→ Bounded Waiting

Waiting
→ Busy Waiting

Main Limitation
→ Not a practical general-purpose modern solution

Modern Alternatives
→ Mutex
→ Semaphore
→ Spinlock
→ Atomic Operations
→ Condition Variables
```

---

# 28. Most Important Interview Formula

Remember this:

```text
flag[i] = true
       ↓
"I want to enter"

turn = j
       ↓
"I'll give the other process priority"

while (flag[j] && turn == j)
       ↓
"If the other wants to enter AND
 it's their turn → WAIT"

Critical Section
       ↓
Do shared-resource work

flag[i] = false
       ↓
"I'm done"
```

### The easiest memory trick

```text
┌────────────┬──────────────────────────┐
│ flag       │ "Do I want to enter?"    │
├────────────┼──────────────────────────┤
│ turn       │ "Who gets priority?"     │
├────────────┼──────────────────────────┤
│ while      │ "Should I wait?"         │
├────────────┼──────────────────────────┤
│ CS         │ "Do shared work"         │
├────────────┼──────────────────────────┤
│ flag=false │ "I'm leaving"            │
└────────────┴──────────────────────────┘
```

> **Placement one-liner:**  
> **Peterson's Algorithm is a classic two-process software solution to the critical-section problem that uses `flag[]` to express intent and `turn` to resolve simultaneous requests, providing mutual exclusion, progress, and bounded waiting under its assumptions.**