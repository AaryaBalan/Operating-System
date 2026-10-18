# Priority Inversion in Operating Systems

## Table of Contents
1. [What Is Priority Inversion?](#1-what-is-priority-inversion)
2. [Understanding the Three Tasks](#2-understanding-the-three-tasks)
3. [How Priority Inversion Happens](#3-how-priority-inversion-happens)
4. [Types of Priority Inversion](#4-types-of-priority-inversion)
5. [Solutions to Priority Inversion](#5-solutions-to-priority-inversion)
6. [Real-World Example](#6-real-world-example)
7. [Priority Inversion vs. Deadlock vs. Starvation](#7-priority-inversion-vs-deadlock-vs-starvation)
8. [Important Interview Questions](#8-important-interview-questions)
9. [Quick Revision](#9-quick-revision)

---

## 1. What Is Priority Inversion?

**Priority inversion** is a scheduling problem in which a high-priority task is forced to wait for a low-priority task because the low-priority task holds a shared resource, such as a mutex or lock.

The problem becomes more serious when a medium-priority task preempts the low-priority task, preventing it from releasing the resource.

### In simple terms

Imagine three people who need to use the same machine:

- **L — Low priority:** Currently using the machine.
- **H — High priority:** Needs the machine urgently.
- **M — Medium priority:** Does not need the machine but can interrupt L.

H must wait for L to finish. However, M keeps interrupting L, so L cannot finish and release the machine.

As a result, H is delayed by M, even though H has a higher priority than both L and M.

This is called **priority inversion**.

### Technical definition

Priority inversion occurs when a higher-priority task is indirectly delayed by a lower-priority task, often because the lower-priority task holds a resource required by the higher-priority task.

## 2. Understanding the Three Tasks

Assume a priority-based preemptive scheduler, where a higher-priority ready task can interrupt a lower-priority running task.

| Task | Priority | Responsibility |
|---|---|---|
| H | High | Needs a shared resource |
| M | Medium | Performs unrelated work |
| L | Low | Currently holds the shared resource |

For this example, the priority order is:

**H > M > L**

The important point is that L holds a lock that H needs.

## 3. How Priority Inversion Happens

Consider this sequence of events:

1. **L starts running** and acquires a mutex to access a shared resource.
2. **H becomes ready** and requests the same mutex.
3. H cannot acquire the mutex because L already holds it, so H becomes blocked.
4. **M becomes ready** and preempts L because M has a higher priority than L.
5. M executes while L cannot run.
6. L cannot finish its critical section or release the mutex until it gets CPU time again.
7. H remains blocked until L releases the mutex.
8. After the mutex is released, H can acquire it and continue.

### Scheduling timeline

```text
Time ----------------------------------------------------->

L:  [Acquire Lock] [Critical Section] [Release Lock]
H:                [Requests Lock] ---- WAIT ---- [Runs]
M:                              [------ Executes ------]

Actual execution:
L runs -> H blocks -> M preempts L -> L resumes -> H runs
```

The key issue is that **M delays L, and L's delay indirectly delays H**.

Without M's interference, L might release the lock quickly, allowing H to proceed sooner.

## 4. Types of Priority Inversion

### 4.1 Bounded Priority Inversion

Bounded priority inversion occurs when the delay experienced by H is limited by a known or predictable amount of time.

For example:

- L acquires a mutex.
- H requests the mutex and becomes blocked.
- M preempts L and executes for a known, limited duration.
- L resumes and releases the mutex.
- H acquires the mutex.

If the time L needs to finish its critical section and the interference from M are both bounded, the resulting delay can be bounded.

**Key point:** The delay has a known upper limit under the assumed scheduling conditions.

### 4.2 Unbounded Priority Inversion

Unbounded priority inversion occurs when H's delay has no guaranteed upper bound under the scheduling conditions.

For example:

- L acquires a mutex.
- H requests the mutex and becomes blocked.
- M becomes ready and preempts L.
- Additional medium-priority work keeps becoming ready.
- L gets little or no CPU time to finish its critical section.
- H continues waiting for L to release the mutex.

The delay may become extremely long or potentially indefinite if L is continually prevented from running.

**Key point:** H's waiting time is not guaranteed to have a finite upper bound.

### Comparison

| Bounded | Unbounded |
|---|---|
| Delay has a known upper limit under the assumed conditions. | No guaranteed upper limit exists. |
| The resource holder eventually gets enough CPU time to release the resource. | The resource holder may be repeatedly preempted. |
| Timing can be analyzed and planned for. | Timing becomes unpredictable. |

**Important distinction:** Priority inversion is not automatically unbounded. The severity depends on the scheduling policy, resource-locking protocol, and execution behavior of the tasks.

## 5. Solutions to Priority Inversion

### 5.1 Priority Inheritance Protocol (PIP)

Priority inheritance temporarily increases the priority of a low-priority task when it holds a resource needed by a higher-priority blocked task.

#### How it works

Suppose:

- L has low priority.
- H has high priority.
- L holds a mutex that H needs.
- H requests the mutex and blocks.

Under priority inheritance, L temporarily inherits H's priority.

Now the effective priority order is:

**L (temporarily boosted to H's priority) > M**

L can preempt M, finish its critical section, and release the mutex. H can then acquire the mutex and resume.

After L releases the relevant resource and no longer needs the inherited priority, its priority returns to its normal level, subject to the protocol's rules.

#### Advantages
- Reduces interference from medium-priority tasks.
- Helps the resource holder release the resource sooner.
- Is useful in real-time systems.

#### Limitation
- It does not eliminate every possible blocking delay, especially when multiple locks or nested resource dependencies are involved.

### 5.2 Priority Ceiling Protocol (PCP)

The priority ceiling protocol assigns a ceiling priority to each shared resource. The ceiling is based on the highest priority of any task that may use that resource.

When a task locks a resource, the protocol uses the resource ceilings to control which tasks may proceed and to limit priority inversion and deadlock risks.

#### Example

Suppose a shared resource is needed by both H and L.

- The resource's ceiling is based on H's priority.
- L locks the resource.
- The protocol applies its resource-access and priority rules to prevent inappropriate interference.
- L can complete the protected operation and release the resource.

**Key point:** PCP uses predefined resource ceilings to control resource access and bound blocking in systems that correctly implement the protocol.

### 5.3 Avoiding Blocking

Another approach is to reduce the need for tasks to wait on shared locks.

Possible techniques include:

- Minimizing the time spent inside critical sections.
- Avoiding unnecessary shared mutable state.
- Using suitable lock-free or non-blocking algorithms.
- Designing tasks so they do not hold locks while performing long operations.
- Using message passing or other synchronization approaches where appropriate.

**Important:** Non-blocking algorithms can reduce lock-related priority inversion, but they do not automatically solve every scheduling or resource-contention problem.

## 6. Real-World Example

### Mars Pathfinder

A well-known real-world example of priority inversion occurred in the Mars Pathfinder mission in 1997.

The problem involved tasks with different priorities and shared resources. A lower-priority task holding a mutex could prevent a higher-priority task from proceeding, while medium-priority tasks interfered with the lower-priority task.

Priority inheritance was used to address the problem.

This example demonstrates why priority inversion matters in real-time and embedded systems: a task that appears less important can indirectly delay a critical task, potentially causing missed deadlines or system failures.

## 7. Priority Inversion vs. Deadlock vs. Starvation

| Feature | Priority Inversion | Deadlock | Starvation |
|---|---|---|---|
| Meaning | A high-priority task is delayed by a lower-priority task. | Tasks wait indefinitely for resources held by one another. | A task repeatedly fails to receive CPU time or resources. |
| Main cause | Resource contention combined with scheduling interference. | Circular waiting or another deadlock condition. | Unfair scheduling or resource allocation. |
| Can execution continue? | Often, yes; the delay may eventually end. | The deadlocked tasks cannot proceed without external intervention or recovery. | Other tasks may continue while one task waits. |
| Common solution | Priority inheritance or priority ceiling protocols. | Deadlock prevention, avoidance, detection, or recovery. | Fair scheduling or fair resource allocation. |

**Remember:** Priority inversion, deadlock, and starvation are different problems, although they can sometimes occur together.

## 8. Important Interview Questions

### Q1. What is priority inversion?

Priority inversion is a scheduling problem in which a high-priority task waits for a resource held by a low-priority task, potentially allowing medium-priority tasks to delay the high-priority task indirectly.

### Q2. Why is priority inversion dangerous?

It can cause high-priority tasks to miss deadlines, particularly in real-time and embedded systems.

### Q3. What are the three tasks involved in the classic example?

- H: High-priority task waiting for a resource.
- M: Medium-priority task that can preempt L.
- L: Low-priority task holding the resource.

### Q4. How does priority inheritance solve the problem?

It temporarily raises the priority of the resource-holding task so it can finish its critical section and release the resource sooner.

### Q5. What is the priority ceiling protocol?

It assigns a ceiling priority to each shared resource and uses resource-access rules to control interference and bound blocking.

### Q6. Does priority inversion always cause deadlock?

No. Priority inversion often ends when the low-priority task gets CPU time and releases the resource. Deadlock is a separate condition in which tasks cannot proceed because of a resource-wait dependency.

### Q7. Where is priority inversion especially important?

Real-time operating systems, embedded systems, robotics, industrial control, and other environments where tasks must meet timing deadlines.

### Q8. What is the difference between bounded and unbounded priority inversion?

Bounded inversion has a known upper limit on the delay under specified assumptions. Unbounded inversion has no guaranteed upper limit under the assumed scheduling conditions.

## 9. Quick Revision

- **Priority inversion:** A high-priority task is indirectly delayed by a lower-priority task.
- **L:** Holds the shared resource.
- **H:** Needs the resource and becomes blocked.
- **M:** Can preempt L and delay resource release.
- **Bounded inversion:** The delay has a known upper limit.
- **Unbounded inversion:** No finite upper limit is guaranteed.
- **Priority inheritance:** Temporarily boosts the resource holder's priority.
- **Priority ceiling:** Uses resource ceiling priorities to control access and blocking.
- **Main risk:** A high-priority task may miss its deadline.

### One-line interview answer

Priority inversion occurs when a high-priority task waits for a resource held by a low-priority task, and medium-priority tasks can delay the low-priority task from releasing that resource. Priority inheritance and priority ceiling protocols help control the problem.
