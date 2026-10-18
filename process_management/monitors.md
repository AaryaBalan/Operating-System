# Monitors in Process Synchronization

> A monitor is a high-level synchronization mechanism that combines shared data, the operations that access that data, and synchronization rules to make concurrent programming safer and easier.

## Table of Contents

1. [What Is a Monitor?](#1-what-is-a-monitor)
2. [Why Do We Need Monitors?](#2-why-do-we-need-monitors)
3. [How Does a Monitor Work?](#3-how-does-a-monitor-work)
4. [Structure of a Monitor](#4-structure-of-a-monitor)
5. [Condition Variables](#5-condition-variables)
6. [Monitor Example: Bank Account](#6-monitor-example-bank-account)
7. [Monitor Implementation in Java](#7-monitor-implementation-in-java)
8. [Monitor vs Semaphore vs Mutex](#8-monitor-vs-semaphore-vs-mutex)
9. [Producer-Consumer Problem Using a Monitor](#9-producer-consumer-problem-using-a-monitor)
10. [Advantages and Limitations](#10-advantages-and-limitations)
11. [Technical Explanation](#11-technical-explanation)
12. [Common Mistakes](#12-common-mistakes)
13. [Placement Interview Questions](#13-placement-interview-questions)
14. [Quick Revision Cheat Sheet](#14-quick-revision-cheat-sheet)

---

## 1. What Is a Monitor?

### Understand it with a simple example

Imagine a bank has a room containing a shared account balance.

Many employees need to access that balance, but only one employee should update it at a time.

The bank establishes three rules:

1. The balance is stored inside the room.
2. All operations on the balance happen through approved procedures.
3. Only one employee can execute those procedures inside the room at a time.

This is the basic idea behind a monitor.

A monitor combines:

- **Shared data:** Information that multiple threads need to access.
- **Monitor procedures:** Operations that read or modify that data.
- **Automatic mutual exclusion:** A synchronization rule that prevents multiple threads from executing inside the monitor simultaneously.
- **Condition variables:** Mechanisms that allow threads to wait until certain conditions become true.

Think of a monitor as a protected room containing both the data and the rules for using it.

### Technical definition

A monitor is a high-level synchronization construct that encapsulates shared state and the procedures operating on that state, while providing mutual exclusion and, typically, condition-based synchronization.

### A simple diagram

```text
          MONITOR
    +----------------------+
    | Shared data          |
    |                      |
    | balance              |
    | availableItems       |
    |                      |
    | Monitor procedures   |
    |                      |
    | deposit()            |
    | withdraw()           |
    |                      |
    | Condition variables  |
    |                      |
    | sufficientFunds      |
    +----------------------+
              |
              v
      One thread executes
      inside at a time
```

**Remember:** A monitor is more than a lock. It groups the data, the operations, and the synchronization rules together.

---

## 2. Why Do We Need Monitors?

Let's start with a shared bank account.

Suppose the account balance is ₹1,000.

Two threads execute:

```java
balance = balance + 500;
balance = balance - 200;
```

Without proper synchronization, their operations may overlap in a way that produces an incorrect balance.

The correct result should be ₹1,300.

### Solution 1: Protect the operations with a lock

Each thread must acquire a lock before accessing the balance and release it afterward.

```text
Acquire lock
    Read balance
    Update balance
Release lock
```

This can work, but the programmer must ensure that the lock is acquired and released correctly everywhere the shared data is accessed.

### Solution 2: Use a monitor

A monitor places the shared balance and its operations together.

```text
Monitor Account
    Shared balance

    deposit(amount)
    withdraw(amount)
```

The monitor controls entry into its procedures, preventing multiple threads from executing those procedures simultaneously.

This makes synchronization easier to organize and reduces the chance of forgetting to protect a shared operation.

### Why are monitors useful?

- They provide mutual exclusion.
- They organize shared data and its operations in one place.
- They reduce the need for programmers to manage low-level locks manually.
- They support waiting for conditions, such as sufficient funds or available buffer space.
- They help prevent race conditions when used correctly.

**Important:** A monitor does not automatically solve every concurrency problem. The programmer must still design the correct conditions and operation order.

---

## 3. How Does a Monitor Work?

Imagine two threads, A and B, trying to execute methods inside the same monitor.

```text
Thread A ───────┐
                |
                v
          +-----------+
          |  Monitor  |
          |           |
          | Shared    |
          | Data      |
          +-----------+
                ^
                |
Thread B ───────┘
```

The monitor provides mutual exclusion.

### Step-by-step execution

**Step 1:** Thread A requests entry into a monitor procedure.

**Step 2:** If the monitor is available, A enters and executes the procedure.

**Step 3:** Thread B requests entry while A is inside.

**Step 4:** B must wait until it can enter the monitor.

**Step 5:** A finishes the procedure and leaves the monitor.

**Step 6:** B can enter and execute its procedure.

```text
Time ─────────────────────────────────────>

Thread A: [ ENTER | WORK | EXIT ]
Thread B:       [ WAITING ] [ ENTER | WORK | EXIT ]
```

The key point is that **only one thread executes inside the monitor at a time**, assuming the monitor's procedures are used as intended.

### What if a thread cannot continue?

Suppose a thread wants to withdraw ₹500, but the account contains only ₹100.

It should not withdraw the money yet.

Instead, it can wait until the balance becomes sufficient.

Monitors use condition variables to support this kind of coordination.

---

## 4. Structure of a Monitor

A textbook monitor typically contains four important parts.

| Component | Purpose | Example |
|---|---|---|
| Shared data | Stores information used by multiple threads | `balance` |
| Monitor procedures | Safely access or update shared data | `deposit()` |
| Initialization | Establishes the initial state | `balance = 0` |
| Condition variables | Allow threads to wait for conditions | `sufficientFunds` |

### Example structure

```text
monitor BankAccount {

    shared data:
        balance = 0

    condition variable:
        sufficientFunds

    procedure deposit(amount):
        balance = balance + amount

    procedure withdraw(amount):
        if balance < amount:
            wait(sufficientFunds)

        balance = balance - amount
}
```

This is illustrative monitor pseudocode, not valid Java code.

It shows the design: the data and the operations that use it belong together, while condition variables coordinate threads that cannot yet proceed.

---

## 5. Condition Variables

### What is a condition variable?

A condition variable allows a thread to wait until some condition may have become true.

For example:

- A consumer waits until a buffer contains an item.
- A producer waits until a buffer has an empty slot.
- A customer waits until the account has enough money for a withdrawal.

A condition variable is not the condition itself. It is a synchronization mechanism used to wait for changes that may make the condition true.

### Example: Waiting for sufficient funds

Suppose:

```text
balance = 100
withdrawal = 500
```

The withdrawal cannot proceed because the balance is insufficient.

The thread waits on a condition variable associated with sufficient funds.

When another thread deposits money, it signals the condition variable. A waiting thread can then wake up and check the balance again.

### Three common condition-variable operations

In textbook monitor notation, the common operations are:

| Operation | Meaning |
|---|---|
| `wait()` | Suspend the current thread and release the monitor so another thread can enter |
| `signal()` | Wake one thread waiting on the condition, if any |
| `broadcast()` | Wake all threads waiting on the condition |

The precise behavior of `signal()` depends on the monitor model. In some textbook models, the awakened thread immediately gets the monitor; in others, it must compete to enter after the signaling thread yields or exits.

### Important: Waking up does not mean the condition is true

Suppose a thread is waiting for:

```text
balance >= withdrawalAmount
```

It wakes up after another thread signals the condition variable.

But another thread might have changed the balance before the awakened thread resumes.

Therefore, the thread must check the condition again.

Textbook monitor pseudocode should normally use:

```text
while balance < amount:
    wait(sufficientFunds)
```

not simply:

```text
if balance < amount:
    wait(sufficientFunds)
```

The `while` loop checks the condition again after waking.

This is a fundamental rule in concurrent programming.

---

## 6. Monitor Example: Bank Account

Let's understand the complete logic.

Suppose a bank account starts with ₹100.

A thread wants to withdraw ₹500.

### Step 1: The withdrawal request arrives

```text
balance = 100
requested withdrawal = 500
```

The balance is insufficient.

### Step 2: The thread waits

The thread cannot withdraw the money, so it waits on the `sufficientFunds` condition variable.

In a correctly implemented monitor, waiting releases the monitor lock so another thread can enter and deposit money.

### Step 3: Another thread deposits ₹500

The balance becomes:

```text
100 + 500 = 600
```

The depositing thread signals the condition variable.

### Step 4: The withdrawal thread wakes up

It checks the condition again:

```text
600 >= 500
```

The condition is true, so the withdrawal can proceed.

### Step 5: The withdrawal completes

```text
600 - 500 = 100
```

The final balance is ₹100.

### Visual flow

```text
Withdrawal thread
        |
        v
Is balance sufficient?
        |
    No  |  Yes
        |
        v
Wait on condition
        |
        v
Deposit thread adds money
        |
        v
Signal condition variable
        |
        v
Withdrawal thread wakes
        |
        v
Check balance again
        |
        v
Withdraw safely
```

**Key idea:** The monitor provides mutual exclusion, while the condition variable allows the thread to wait until the required condition can be satisfied.

---

## 7. Monitor Implementation in Java

Java provides built-in monitor-like synchronization through every object's intrinsic monitor.

Java does not have a dedicated `monitor` keyword. Instead, the `synchronized` keyword allows code to acquire an object's monitor.

### 7.1 A basic Java example

```java
class Account {
    private int balance = 0;

    public synchronized void deposit(int amount) {
        balance += amount;
    }

    public synchronized void withdraw(int amount) {
        balance -= amount;
    }

    public synchronized int getBalance() {
        return balance;
    }
}
```

### Explanation

**`private int balance = 0;`**

The account balance is shared state.

**`public synchronized void deposit(int amount)`**

A thread must acquire this object's monitor before executing the method.

**`public synchronized void withdraw(int amount)`**

This method uses the same object's monitor, so two threads cannot simultaneously execute these synchronized instance methods on the same account object.

**`getBalance()`**

This synchronized method also accesses the balance while holding the object's monitor.

### What does `synchronized` actually do?

For a synchronized instance method, Java acquires the monitor associated with `this`.

For a synchronized block:

```java
synchronized (lockObject) {
    // Protected operations
}
```

Java acquires the monitor associated with `lockObject`.

When the synchronized method or block exits, the monitor is released, including when it exits by throwing an exception.

### Important limitation of this example

The code above prevents concurrent execution of the synchronized methods on the same object, but it does **not** prevent an account from going negative.

It needs an additional balance check and a waiting strategy if withdrawals must wait for sufficient funds.

### 7.2 A more complete bank account monitor

```java
class Account {
    private int balance = 0;

    public synchronized void deposit(int amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException(
                "Amount must be positive"
            );
        }

        balance += amount;

        // Notify threads that may be waiting for funds.
        notifyAll();
    }

    public synchronized void withdraw(int amount)
            throws InterruptedException {

        if (amount <= 0) {
            throw new IllegalArgumentException(
                "Amount must be positive"
            );
        }

        while (balance < amount) {
            wait();
        }

        balance -= amount;
    }

    public synchronized int getBalance() {
        return balance;
    }
}
```

### Understand the important lines

**`synchronized`**

The method acquires the account object's monitor before accessing the balance.

**`while (balance < amount)`**

The withdrawal thread checks whether enough money exists. If not, it waits and checks again after waking.

**`wait()`**

Java's `Object.wait()` temporarily releases the object's monitor and suspends the thread. The thread must reacquire the monitor before returning from `wait()`.

**`notifyAll()`**

The depositing thread wakes threads waiting on the same object monitor. They must reacquire the monitor before continuing.

**`throws InterruptedException`**

Java allows a waiting thread to be interrupted, so the method must handle or declare this exception.

### Why use `while` instead of `if`?

Imagine the account has ₹500, and two threads each want to withdraw ₹400.

Both may be waiting for funds earlier. A deposit can wake both threads, but only one can acquire the monitor at a time.

The first thread withdraws ₹400, leaving ₹100.

When the second thread resumes, the balance is no longer sufficient.

The `while` loop makes the second thread wait again instead of performing an invalid withdrawal.

### Java's `wait()` and `notify()` versus textbook condition variables

This distinction is important for interviews.

In textbook monitor notation:

```text
wait(sufficientFunds)
signal(sufficientFunds)
```

The condition variable is named `sufficientFunds`.

In Java, the intrinsic monitor API uses:

```java
wait();
notify();
notifyAll();
```

These methods operate on the object's monitor wait set. They do not name a separate condition variable like the textbook example.

Also, `notify()` does not immediately release the monitor. The notifying thread continues to hold it until it exits the synchronized region or otherwise releases the monitor.

Java's `wait()` must be called while holding that object's monitor; otherwise, it throws `IllegalMonitorStateException`.

---

## 8. Monitor vs Semaphore vs Mutex

These three concepts are related but different.

| Feature | Monitor | Semaphore | Mutex |
|---|---|---|---|
| Main purpose | Encapsulate shared data and synchronize its operations | Manage permits and coordinate tasks | Enforce mutual exclusion |
| Abstraction level | High-level construct | Synchronization primitive | Locking primitive |
| Mutual exclusion | Usually built in | Can be achieved with a binary semaphore | Main purpose |
| Shared data organization | Data and procedures are grouped together | Does not inherently encapsulate shared data | Does not inherently encapsulate shared data |
| Waiting for a condition | Typically uses condition variables | Can use permits for synchronization or signaling | Usually not its primary purpose |
| Ownership | Depends on the monitor model and implementation | Usually does not enforce ownership | Typically enforces ownership |
| Example | Java synchronized object | Semaphore with five permits | Java `Lock` or a mutex in another language |

### Easy way to remember

- **Monitor:** A protected room containing shared data and the operations that use it.
- **Semaphore:** A system of permits controlling how many threads can proceed or coordinating events.
- **Mutex:** A lock that grants exclusive access to one owner at a time.

A monitor may use a lock internally. A semaphore may also be implemented using lower-level atomic operations and operating-system facilities.

---

## 9. Producer-Consumer Problem Using a Monitor

The producer-consumer problem is a classic OS synchronization question.

Imagine a buffer that can hold three items.

- The producer inserts items.
- The consumer removes items.
- The producer must wait if the buffer is full.
- The consumer must wait if the buffer is empty.

A monitor can encapsulate the buffer and enforce the correct rules.

### Pseudocode

```text
monitor BoundedBuffer {

    buffer
    capacity = N

    condition notFull
    condition notEmpty

    procedure put(item):

        while buffer is full:
            wait(notFull)

        insert item into buffer
        signal(notEmpty)

    procedure get():

        while buffer is empty:
            wait(notEmpty)

        item = remove item from buffer
        signal(notFull)

        return item
}
```

This is conceptual monitor pseudocode. It assumes a monitor model in which `wait()` releases the monitor while the caller is waiting and the condition is checked again after waking.

### How does it work?

**When the buffer is full:**

The producer waits on `notFull`. The consumer can enter the monitor and remove an item.

**When an item is removed:**

The consumer signals `notFull`, allowing a producer to resume and check the buffer.

**When the buffer is empty:**

The consumer waits on `notEmpty`. The producer can enter the monitor and insert an item.

**When an item is inserted:**

The producer signals `notEmpty`, allowing a consumer to resume and check the buffer.

### Why is a monitor helpful here?

The buffer, its capacity, and its operations are kept together. The monitor controls access to the buffer, while condition variables handle the full and empty cases.

This is easier to reason about than scattering low-level lock operations throughout the program.

---

## 10. Advantages and Limitations

### Advantages

1. **Automatic mutual exclusion:** The monitor manages entry into its protected procedures.
2. **Encapsulation:** Shared data and the operations that access it are grouped together.
3. **Safer programming:** The structure helps prevent accidental unprotected access.
4. **Condition-based synchronization:** Threads can wait for events or conditions.
5. **Readable design:** The synchronization rules are easier to understand when centralized.

### Limitations

1. **Language or library dependence:** The exact monitor features depend on the language and runtime.
2. **Incorrect condition handling:** Poorly designed conditions can cause incorrect behavior or indefinite waiting.
3. **Deadlock remains possible:** Threads can still wait indefinitely because of incorrect synchronization design.
4. **Potential contention:** Only one thread can execute inside a traditional monitor at a time.
5. **Performance overhead:** Lock acquisition, waiting, waking, and scheduling may have costs.
6. **Limited parallelism inside the monitor:** Long-running procedures can prevent other threads from entering, even if some work could safely occur concurrently.

### Correcting a common misconception

It is too strong to say that monitors can only be implemented through a language keyword or that they are inherently nonportable.

Monitors can be provided through language features, runtime libraries, or combinations of lower-level synchronization primitives. Java provides monitor-like behavior through intrinsic object monitors and `synchronized`; other languages and libraries offer different abstractions.

---

## 11. Technical Explanation

### 11.1 Mutual exclusion

In a traditional monitor, only one thread executes inside the monitor at a time.

This protects the monitor's internal shared state from concurrent execution of its procedures.

### 11.2 Condition synchronization

Mutual exclusion alone is not enough.

For example, a thread can safely acquire a monitor and discover that the buffer is empty. It must then wait for an item rather than repeatedly holding the monitor while checking.

A condition variable allows the thread to wait while releasing the monitor, so another thread can enter and change the state.

### 11.3 Why condition checks must be repeated

The condition can change between the time a thread is awakened and the time it resumes execution.

Other threads may acquire the monitor first and modify the shared state.

Therefore, the correct pattern is generally:

```text
while condition is false:
    wait(conditionVariable)

perform operation
```

This protects against the condition still being false when the thread resumes.

### 11.4 Hoare-style and Mesa-style monitors

Operating Systems theory commonly distinguishes two monitor signaling models.

**Hoare-style monitors**

When a thread signals a condition, the awakened thread immediately gets control of the monitor under the classical model. The signaling thread waits or yields control.

**Mesa-style monitors**

Signaling makes a waiting thread eligible to run, but the signaling thread may continue executing. The awakened thread must later reacquire the monitor.

Java's intrinsic monitor behavior is closer to the Mesa-style model: `notify()` or `notifyAll()` wakes waiting threads, but the notifying thread retains the monitor until it releases it.

This is another reason condition checks should normally be placed inside a `while` loop.

### 11.5 Monitor versus a synchronized Java method

A synchronized Java method is a practical way to use an object's intrinsic monitor.

However, a full textbook monitor is a conceptual synchronization construct that also includes the monitor's shared state, procedures, and condition synchronization.

A Java class is not automatically a complete monitor merely because it contains a synchronized method. Correct encapsulation and synchronization discipline still matter.

---

## 12. Common Mistakes

### Mistake 1: Thinking a monitor is just a lock

A lock provides mutual exclusion. A monitor combines mutual exclusion with encapsulated shared state, procedures, and typically condition synchronization.

### Mistake 2: Confusing monitor condition variables with Java methods

Textbook monitors use condition variables with operations such as `wait(condition)` and `signal(condition)`.

Java's `Object.wait()`, `notify()`, and `notifyAll()` work with an object's intrinsic monitor and its wait set.

### Mistake 3: Assuming `notify()` immediately transfers the lock

In Java, the notifying thread keeps the monitor until it releases it. The awakened thread must reacquire the monitor before continuing.

### Mistake 4: Using `if` instead of `while` around a wait

The condition might no longer be true when the thread resumes. Recheck it in a loop.

### Mistake 5: Assuming monitors prevent deadlock automatically

Monitors simplify synchronization, but bad locking designs can still cause deadlock.

### Mistake 6: Assuming `synchronized` prevents every race condition

It protects only operations coordinated by the relevant monitor. Unsynchronized access to the same shared state can still cause problems.

---

## 13. Placement Interview Questions

### Q1. What is a monitor in Operating Systems?

A monitor is a high-level synchronization construct that encapsulates shared data and the operations that access it, while providing mutual exclusion and condition-based synchronization.

### Q2. What are the main components of a monitor?

Shared data, monitor procedures, initialization, and condition variables.

### Q3. Why are monitors considered safer than semaphores?

Monitors organize the shared state and its operations together and typically manage mutual exclusion automatically. Semaphores require programmers to design and place wait and signal operations correctly.

### Q4. What is a condition variable?

A synchronization mechanism that allows a thread to wait until a condition may have become true.

### Q5. What do `wait()`, `signal()`, and `broadcast()` mean in textbook monitor notation?

`wait()` suspends the caller and releases the monitor while waiting; `signal()` wakes one waiting thread; `broadcast()` wakes all waiting threads.

### Q6. How are monitors implemented in Java?

Java provides intrinsic object monitors through the `synchronized` keyword, together with `wait()`, `notify()`, and `notifyAll()`.

### Q7. Does Java have a `monitor` keyword?

No. Java provides monitor-like synchronization through object monitors and `synchronized`.

### Q8. Why does Java's `wait()` release the monitor?

It allows another thread to acquire the monitor and change the condition the waiting thread needs. The waiting thread must reacquire the monitor before continuing.

### Q9. Why should `wait()` usually be inside a `while` loop?

Because the condition may still be false when the thread resumes, or another thread may change the state before it reacquires the monitor.

### Q10. What is the difference between a monitor and a semaphore?

A monitor encapsulates shared data and operations with mutual exclusion and condition synchronization. A semaphore manages permits or signals using atomic wait and signal operations.

### Q11. Can a monitor lead to deadlock?

Yes. Poorly designed synchronization or circular dependencies can still cause deadlock.

### Q12. What is the producer-consumer problem?

A synchronization problem in which producers add items to a bounded buffer and consumers remove them, while avoiding insertion into a full buffer and removal from an empty one.

### Q13. What is the difference between Hoare-style and Mesa-style monitors?

Hoare-style signaling transfers monitor control immediately to the awakened thread under the classical model. Mesa-style signaling makes the waiting thread eligible to run later, so it must reacquire the monitor and recheck the condition.

### Q14. Does a synchronized method protect every access to a variable?

No. It protects the method using its associated monitor. Other accesses must follow a compatible synchronization discipline.

### Q15. What is the biggest advantage of a monitor?

It combines shared data, the operations that use it, and synchronization rules into one organized abstraction.

---

## 14. Quick Revision Cheat Sheet

| Concept | Meaning |
|---|---|
| Monitor | High-level synchronization construct |
| Shared data | State accessed by multiple threads |
| Monitor procedure | Operation performed within the monitor |
| Mutual exclusion | Only one thread executes inside the traditional monitor at a time |
| Condition variable | Mechanism for waiting for a condition |
| `wait()` | Releases the monitor while waiting, then reacquires it before returning |
| `signal()` | Wakes one waiting thread in textbook monitor notation |
| `broadcast()` | Wakes all waiting threads in textbook monitor notation |
| Java `synchronized` | Acquires an object's intrinsic monitor |
| Java `notify()` | Wakes one thread waiting on that object's monitor |
| Java `notifyAll()` | Wakes all threads waiting on that object's monitor |
| Deadlock | Threads wait indefinitely because of a synchronization dependency |
| Hoare monitor | Classical immediate handoff model |
| Mesa monitor | Awakened threads must compete to reacquire the monitor |

### The 30-second explanation

A monitor is a high-level synchronization mechanism that combines shared data, the procedures that operate on it, and synchronization rules.

A traditional monitor ensures that only one thread executes inside it at a time. Condition variables allow threads to wait until conditions become true, while signaling allows waiting threads to resume.

Java implements monitor-like behavior through `synchronized`, `wait()`, `notify()`, and `notifyAll()`. Monitors make synchronization easier to organize than manually managed semaphores, but they do not automatically prevent deadlocks or incorrect condition handling.

**Final takeaway:** A monitor is a protected structure that combines shared data, synchronized operations, and condition-based waiting. It helps make concurrent programs easier to write and reason about.