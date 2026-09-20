# CPU Scheduling in Operating Systems

CPU Scheduling is one of the **most important Operating System topics for placements**.

Before learning algorithms such as **FCFS, SJF, SRTF, Round Robin, and Priority Scheduling**, first understand **why scheduling is needed**.

---

## 📌 Table of Contents

* [1. What is CPU Scheduling?](#1-what-is-cpu-scheduling)
* [2. Simple Definition](#2-simple-definition)
* [3. Why Do We Need CPU Scheduling?](#3-why-do-we-need-cpu-scheduling)
* [4. Important Idea: Ready Queue](#4-important-idea-ready-queue)
* [5. The Basic Process Flow](#5-the-basic-process-flow)
* [6. Important CPU Scheduling Terminology](#6-important-cpu-scheduling-terminology)
* [7. Arrival Time (AT)](#7-arrival-time-at)
* [8. Burst Time (BT)](#8-burst-time-bt)
* [9. Completion Time (CT)](#9-completion-time-ct)
* [10. Turnaround Time (TAT)](#10-turnaround-time-tat)
* [11. Waiting Time (WT)](#11-waiting-time-wt)
* [12. Response Time (RT)](#12-response-time-rt)
* [13. The Most Important Formulas](#13-the-most-important-formulas)
* [14. One Complete Example](#14-one-complete-example)
* [15. CPU Scheduling Goals](#15-cpu-scheduling-goals)
* [16. Throughput](#16-throughput)
* [17. Turnaround Time](#17-turnaround-time)
* [18. Waiting Time](#18-waiting-time)
* [19. Response Time](#19-response-time)
* [20. Preemptive vs Non-Preemptive Scheduling](#20-preemptive-vs-non-preemptive-scheduling)
* [21. Non-Preemptive Scheduling](#21-non-preemptive-scheduling)
* [22. Preemptive Scheduling](#22-preemptive-scheduling)
* [23. Simple Preemptive Example](#23-simple-preemptive-example)
* [24. Quick Comparison](#24-quick-comparison)
* [25. CPU Scheduling Algorithms](#25-cpu-scheduling-algorithms)
* [26. FCFS — First Come First Serve](#26-fcfs--first-come-first-serve)
* [27. FCFS Example](#27-fcfs-example)
* [28. SJF — Shortest Job First](#28-sjf--shortest-job-first)
* [29. Why Is SJF Important?](#29-why-is-sjf-important)
* [30. SJF Starvation Example](#30-sjf-starvation-example)
* [31. SRTF — Shortest Remaining Time First](#31-srtf--shortest-remaining-time-first)
* [32. SRTF Example](#32-srtf-example)
* [33. SJF vs SRTF](#33-sjf-vs-srtf)
* [34. Round Robin (RR)](#34-round-robin-rr)
* [35. Round Robin Example](#35-round-robin-example)
* [36. Why Is Round Robin Fair?](#36-why-is-round-robin-fair)
* [37. Time Quantum](#37-time-quantum)
* [38. Priority Scheduling](#38-priority-scheduling)
* [39. Priority Scheduling and Starvation](#39-priority-scheduling-and-starvation)
* [40. Aging](#40-aging)
* [41. HRRN — Highest Response Ratio Next](#41-hrrn--highest-response-ratio-next)
* [42. Why Does HRRN Help?](#42-why-does-hrrn-help)
* [43. Multilevel Queue Scheduling](#43-multilevel-queue-scheduling)
* [44. Multilevel Feedback Queue (MLFQ)](#44-multilevel-feedback-queue-mlfq)
* [45. Important Comparison](#45-important-comparison)
* [46. Gantt Chart](#46-gantt-chart)
* [47. Placement Problem — FCFS](#47-placement-problem--fcfs)
* [48. How to Solve Scheduling Problems](#48-how-to-solve-scheduling-problems)
* [49. Important Placement Trick](#49-important-placement-trick)
* [50. Placement Question — SRTF](#50-placement-question--srtf)
* [51. Another Placement Question](#51-another-placement-question)
* [52. Placement Question — SRTF Waiting Time](#52-placement-question--srtf-waiting-time)
* [53. Common Placement Traps](#53-common-placement-traps)
* [54. Most Important Algorithms to Master for Placements](#54-most-important-algorithms-to-master-for-placements)
* [55. Technical Explanation](#55-technical-explanation)
* [56. Technical View of a Context Switch](#56-technical-view-of-a-context-switch)
* [57. Technical View of Scheduling Metrics](#57-technical-view-of-scheduling-metrics)
* [58. Interview Questions](#58-interview-questions)
* [59. Intermediate Interview Questions](#59-intermediate-interview-questions)
* [60. Rapid Revision Sheet](#60-rapid-revision-sheet)
* [61. The Most Important Mental Model](#61-the-most-important-mental-model)
* [62. Final Placement Strategy](#62-final-placement-strategy)

---

# 1. What is CPU Scheduling?

Imagine you have one teacher and many students.

```text
             Teacher
                |
       ┌────────┼────────┐
       ↓        ↓        ↓
     Student  Student  Student
       A        B        C
```

All students want the teacher's attention.

But the teacher can normally talk to only one student at a time.

The teacher has to decide:

> "Who should I help first?"

The CPU has a similar problem.

There may be many processes:

```text
Chrome
VS Code
Spotify
Terminal
File Explorer
```

But a particular CPU core can execute only one instruction stream at a time.

So the Operating System has to decide:

> **Which process should get the CPU next?**

That decision-making process is called **CPU Scheduling**.

---

# 2. Simple Definition

> **CPU Scheduling is the process used by the Operating System to decide which ready process should get the CPU next.**

The main goals include:

* Keep the CPU busy
* Reduce waiting time
* Reduce response time
* Complete more processes efficiently
* Provide reasonable fairness

---

# 3. Why Do We Need CPU Scheduling?

Suppose three processes are waiting:

```text
P1
P2
P3
```

The CPU cannot give all three processes the CPU at exactly the same time on a single core.

So the OS creates a scheduling decision:

```text
Ready Queue

P1
P2
P3

       ↓

   Scheduler

       ↓

     CPU
```

For example:

```text
P1 → CPU
P2 → waits
P3 → waits
```

After P1 finishes or is interrupted:

```text
P2 → CPU
```

Then:

```text
P3 → CPU
```

The rule used to decide this order is called a **CPU Scheduling Algorithm**.

---

# 4. Important Idea: Ready Queue

A process that is ready to execute but is waiting for the CPU is placed in the **Ready Queue**.

For example:

```text
             Ready Queue
        ┌──────────────────┐
        │ P1               │
        │ P2               │
        │ P3               │
        │ P4               │
        └────────┬─────────┘
                 ↓
             Scheduler
                 ↓
                CPU
```

The scheduler chooses one process from the ready queue.

---

# 5. The Basic Process Flow

A process generally follows a flow like:

```text
             NEW
              |
              ↓
            READY
              |
              ↓
           RUNNING
           /     \
          /       \
       I/O         Finished
        ↓              ↓
     WAITING       TERMINATED
        |
        ↓
      READY
```

The scheduler mainly decides:

```text
READY → RUNNING
```

---

# 6. Important CPU Scheduling Terminology

Before learning algorithms, you **must understand these terms**.

These terms appear constantly in placement questions.

---

# 7. Arrival Time (AT)

**Arrival Time** is the time at which a process enters the ready queue.

### Example

Suppose:

```text
P1 arrives at 0 ms
P2 arrives at 2 ms
P3 arrives at 5 ms
```

Then:

| Process | Arrival Time |
| ------- | -----------: |
| P1      |         0 ms |
| P2      |         2 ms |
| P3      |         5 ms |

Think:

> **When did the process become available to run?**

---

# 8. Burst Time (BT)

**Burst Time** is the amount of CPU time required by a process.

Example:

```text
P1 needs 5 ms of CPU
P2 needs 3 ms of CPU
P3 needs 8 ms of CPU
```

Then:

| Process | Burst Time |
| ------- | ---------: |
| P1      |       5 ms |
| P2      |       3 ms |
| P3      |       8 ms |

Think:

> **How much CPU time does this process need?**

---

# 9. Completion Time (CT)

**Completion Time** is the time at which a process completely finishes execution.

Example:

```text
P1 starts at 0 ms
P1 needs 5 ms

0 ───────── 5
     P1
```

P1 completes at:

```text
CT = 5 ms
```

---

# 10. Turnaround Time (TAT)

Turnaround Time tells us:

> **How much total time did the process spend in the system?**

Formula:

```text
TAT = Completion Time - Arrival Time
```

### Example

Suppose:

```text
Arrival Time = 2 ms
Completion Time = 10 ms
```

Then:

```text
TAT = 10 - 2
    = 8 ms
```

So the process spent **8 ms** from arrival until completion.

---

# 11. Waiting Time (WT)

Waiting Time tells us:

> **How much time did the process spend waiting for the CPU?**

Formula:

```text
WT = Turnaround Time - Burst Time
```

or:

```text
WT = TAT - BT
```

### Example

Suppose:

```text
TAT = 10 ms
BT  = 4 ms
```

Then:

```text
WT = 10 - 4
   = 6 ms
```

So the process spent **6 ms waiting**.

---

# 12. Response Time (RT)

Response Time is:

> **The time from when a process arrives until it first gets CPU service.**

Formula:

```text
RT = First CPU Start Time - Arrival Time
```

### Example

Suppose:

```text
Process arrives at 2 ms
First gets CPU at 5 ms
```

Then:

```text
RT = 5 - 2
   = 3 ms
```

The process had to wait 3 ms before receiving its first CPU service.

---

# 13. The Most Important Formulas

For placements, remember these:

```text
┌─────────────────────────────────────────┐
│ Turnaround Time                          │
│                                         │
│ TAT = Completion Time - Arrival Time    │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ Waiting Time                             │
│                                         │
│ WT = Turnaround Time - Burst Time        │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ Response Time                            │
│                                         │
│ RT = First Start Time - Arrival Time    │
└─────────────────────────────────────────┘
```

### Easy way to remember

```text
Arrival
   ↓
   ├──── Waiting ────┐
   ↓                 ↓
CPU execution → Completion
```

---

# 14. One Complete Example

Suppose:

```text
Arrival Time = 0
Burst Time   = 5
```

And the process starts immediately:

```text
0 ───────────── 5
      P1
```

Therefore:

```text
Completion Time = 5

Turnaround Time
= CT - AT
= 5 - 0
= 5

Waiting Time
= TAT - BT
= 5 - 5
= 0

Response Time
= Start Time - Arrival Time
= 0 - 0
= 0
```

So:

| Metric | Value |
| ------ | ----: |
| AT     |     0 |
| BT     |     5 |
| CT     |     5 |
| TAT    |     5 |
| WT     |     0 |
| RT     |     0 |

---

# 15. CPU Scheduling Goals

A scheduling algorithm is evaluated using several factors.

---

## CPU Utilization

CPU utilization tells us how much of the time the CPU is busy.

Example:

```text
CPU busy = 80%
CPU idle = 20%
```

Higher CPU utilization is generally desirable.

The theoretical range is:

```text
0% → 100%
```

Actual utilization depends on workload and system behavior.

---

# 16. Throughput

Throughput means:

> **How many processes are completed per unit of time.**

Example:

```text
10 processes completed
in 5 seconds
```

Then:

```text
Throughput = 10 / 5
           = 2 processes/second
```

Higher throughput generally means more work is being completed.

---

# 17. Turnaround Time

Turnaround time measures:

> **How long a process takes from arrival until completion.**

```text
TAT = CT - AT
```

A smaller turnaround time is generally desirable.

---

# 18. Waiting Time

Waiting time measures how long processes spend waiting for CPU service in the ready queue.

```text
WT = TAT - BT
```

A scheduling algorithm can change waiting time by changing the order in which processes receive CPU time.

---

# 19. Response Time

Response time is particularly important for interactive systems.

Imagine clicking a button.

You do not necessarily care only about when the entire task finishes.

You also care about:

> "How quickly did the system start responding?"

For example:

```text
Click button
   ↓
0 ms
   ↓
100 ms → First response
   ↓
5000 ms → Entire task finishes
```

Response time:

```text
100 ms
```

Turnaround time:

```text
5000 ms
```

These are different measurements.

---

# 20. Preemptive vs Non-Preemptive Scheduling

This is a **very important placement topic**.

There are two broad approaches:

```text
1. Preemptive Scheduling
2. Non-Preemptive Scheduling
```

---

# 21. Non-Preemptive Scheduling

In non-preemptive scheduling, once a process gets the CPU, the scheduler generally does not forcibly take the CPU away.

The process keeps the CPU until:

```text
Process finishes
        OR
Process blocks/waits
```

Example:

```text
P1 → Running
      |
      |
      ↓
   Finishes
      |
      ↓
P2 → Running
```

### Simple Example

Imagine one person is using a bathroom.

They enter:

```text
Person A → Bathroom
```

Someone else cannot simply force them out because another person has arrived.

Person A leaves:

```text
Person A → Leaves
Person B → Enters
```

That is similar to the basic idea of **non-preemptive scheduling**.

---

# 22. Preemptive Scheduling

In preemptive scheduling, the OS can interrupt a running process and give the CPU to another process.

Example:

```text
P1 → Running
      |
      ↓
P2 becomes more important
      |
      ↓
P1 → Ready
      |
      ↓
P2 → Running
```

The currently running process can be temporarily stopped.

---

# 23. Simple Preemptive Example

Suppose:

```text
P1 is running
```

After some time:

```text
P2 arrives
```

If the scheduling algorithm decides P2 should run:

```text
P1 → Running
       ↓
    Preempted
       ↓
P1 → Ready

P2 → Running
```

Later:

```text
P2 → Finished
       ↓
P1 → Running again
```

---

# 24. Quick Comparison

| Feature                           | Preemptive                             | Non-Preemptive           |
| --------------------------------- | -------------------------------------- | ------------------------ |
| Can OS interrupt running process? | Yes                                    | Generally no             |
| Better responsiveness             | Usually                                | Usually lower            |
| Context switches                  | Potentially more                       | Potentially fewer        |
| Complexity                        | Higher                                 | Simpler                  |
| Examples                          | SRTF, Round Robin, preemptive Priority | FCFS, non-preemptive SJF |

---

# 25. CPU Scheduling Algorithms

The important algorithms in the provided material are:

```text
1. FCFS
2. SJF
3. SRTF
4. Round Robin
5. Priority Scheduling
6. HRRN
7. Multilevel Queue
8. Multilevel Feedback Queue
```

Let's understand each one.

---

# 26. FCFS — First Come First Serve

FCFS means:

> **The process that arrives first gets the CPU first.**

Think of a normal queue at a shop:

```text
Person A
Person B
Person C
```

A comes first.

So:

```text
A → B → C
```

Similarly:

```text
P1 arrives first
P2 arrives second
P3 arrives third
```

CPU order:

```text
P1 → P2 → P3
```

---

# 27. FCFS Example

Suppose:

| Process | Arrival Time | Burst Time |
| ------- | -----------: | ---------: |
| P1      |            0 |          5 |
| P2      |            1 |          3 |
| P3      |            2 |          2 |

Since P1 arrives first:

```text
0      5      8      10
|  P1  |  P2  |  P3  |
```

Execution order:

```text
P1 → P2 → P3
```

FCFS is simple.

But it can cause a problem called the **Convoy Effect**.

A long process can make many short processes wait behind it.

Example:

```text
Long process
     ↓
P1 ──────────────────
P2 → waiting
P3 → waiting
P4 → waiting
```

---

# 28. SJF — Shortest Job First

SJF means:

> **Choose the process with the smallest CPU burst time.**

Suppose:

```text
P1 = 8 ms
P2 = 3 ms
P3 = 5 ms
```

SJF chooses:

```text
P2 → P3 → P1
```

because:

```text
3 < 5 < 8
```

---

# 29. Why Is SJF Important?

SJF is famous because, under the standard assumptions, it gives the **minimum average waiting time among non-preemptive scheduling choices when the required CPU burst lengths are known**.

This is a major placement fact.

But there is a problem:

> **Starvation can occur.**

---

# 30. SJF Starvation Example

Suppose a long process is waiting:

```text
P1 = 20 ms
```

But short processes keep arriving:

```text
P2 = 2 ms
P3 = 1 ms
P4 = 3 ms
P5 = 2 ms
...
```

If shorter jobs keep getting selected:

```text
P1
 ↓
wait
 ↓
wait
 ↓
wait
```

P1 may wait for a very long time.

This is called **starvation**.

---

# 31. SRTF — Shortest Remaining Time First

SRTF is the **preemptive version of SJF**.

Instead of asking:

> "Which process has the shortest total burst?"

we ask:

> **"Which process has the shortest remaining CPU time?"**

---

# 32. SRTF Example

Suppose:

```text
P1 arrives at 0
Burst = 8
```

At time 0:

```text
P1 → Running
```

Now at time 2:

```text
P2 arrives
Burst = 3
```

P1 has:

```text
8 - 2 = 6 ms remaining
```

P2 needs:

```text
3 ms
```

Therefore:

```text
P2 < P1 remaining time
```

So P1 is preempted:

```text
P1 → Ready
P2 → Running
```

That is the key idea behind SRTF.

---

# 33. SJF vs SRTF

| SJF                                                        | SRTF                             |
| ---------------------------------------------------------- | -------------------------------- |
| Non-preemptive                                             | Preemptive                       |
| Uses shortest CPU burst                                    | Uses shortest remaining CPU time |
| Running process generally continues until completion/block | Running process can be preempted |
| Can cause starvation                                       | Can also cause starvation        |

Remember:

```text
SJF  = Shortest Job
SRTF = Shortest Remaining Time
```

---

# 34. Round Robin (RR)

Round Robin is especially important for interactive/time-sharing systems.

Imagine students sharing one computer.

Each student gets:

```text
5 minutes
```

Then the next student gets 5 minutes.

Then the next.

Then back to the first.

That fixed amount of time is called the **Time Quantum**.

---

# 35. Round Robin Example

Suppose:

```text
Time Quantum = 2 ms
```

Processes:

```text
P1 = 5 ms
P2 = 3 ms
P3 = 4 ms
```

The CPU might execute:

```text
P1 → 2 ms
P2 → 2 ms
P3 → 2 ms
P1 → 2 ms
P2 → 1 ms
P3 → 2 ms
P1 → 1 ms
```

Conceptually:

```text
| P1 | P2 | P3 | P1 | P2 | P3 | P1 |
  2    2    2    2    1    2    1
```

Every process gets a turn.

---

# 36. Why Is Round Robin Fair?

Suppose:

```text
P1
P2
P3
```

Instead of:

```text
P1 ───────────────
P2 waits
P3 waits
```

we do:

```text
P1 → P2 → P3 → P1 → P2 → P3
```

This gives each runnable process a chance to execute.

---

# 37. Time Quantum

The size of the time quantum matters.

### Very Small Quantum

```text
P1 → P2 → P3 → P1 → P2 → P3 ...
```

Many switches can occur.

This can increase context-switch overhead.

### Very Large Quantum

Round Robin starts behaving more like FCFS because processes keep the CPU for long periods.

So:

```text
Small quantum
→ more responsiveness
→ potentially more context-switch overhead

Large quantum
→ fewer context switches
→ less responsive sharing
```

---

# 38. Priority Scheduling

Priority Scheduling selects a process based on priority.

Example:

```text
P1 → Priority 3
P2 → Priority 1
P3 → Priority 2
```

If **1 means highest priority**, execution could be:

```text
P2 → P3 → P1
```

### Important

Different systems can define whether:

```text
smaller number = higher priority
```

or:

```text
larger number = higher priority
```

Always check the question's convention.

---

# 39. Priority Scheduling and Starvation

Suppose:

```text
P1 = low priority
```

And high-priority processes keep arriving:

```text
P2 = high
P3 = high
P4 = high
P5 = high
...
```

P1 may keep waiting.

This is another example of **Starvation**.

---

# 40. Aging

A common technique to reduce starvation is **Aging**.

The priority of a process can gradually increase the longer it waits.

Example:

```text
Initially:

P1 → Priority 10

After waiting:
P1 → Priority 9

After more waiting:
P1 → Priority 8

After more waiting:
P1 → Priority 7
```

Eventually, the waiting process gets a better chance to run.

The exact direction and numbering depend on the priority convention.

---

# 41. HRRN — Highest Response Ratio Next

HRRN stands for:

> **Highest Response Ratio Next**

It tries to balance:

* Short jobs
* Long-waiting jobs

The response ratio is commonly calculated as:

```text
Response Ratio = (Waiting Time + Burst Time) / Burst Time
```

or:

```text
Response Ratio = 1 + (Waiting Time / Burst Time)
```

The process with the highest response ratio is selected.

---

# 42. Why Does HRRN Help?

Suppose:

```text
P1 has a short burst
P2 has been waiting for a long time
```

SJF may keep preferring short jobs.

HRRN increases the response ratio of processes that have waited longer.

So waiting time becomes part of the decision.

This can reduce starvation compared with simple SJF.

---

# 43. Multilevel Queue Scheduling

In Multilevel Queue Scheduling, processes are separated into different queues.

For example:

```text
              CPU
               ↑
        ┌──────┴──────┐
        │             │
   System Queue   User Queue
        │             │
   High Priority   Lower Priority
```

Different queues can have different scheduling policies.

For example:

```text
Foreground Queue → Round Robin
Background Queue → FCFS
```

The exact structure depends on the system/design.

---

# 44. Multilevel Feedback Queue (MLFQ)

MLFQ is more flexible.

A process can **move between queues** depending on its behavior and scheduling rules.

Conceptually:

```text
          High Priority
               ↓
        +--------------+
        | Queue 1      |
        +--------------+
               ↓
        +--------------+
        | Queue 2      |
        +--------------+
               ↓
        +--------------+
        | Queue 3      |
        +--------------+
          Low Priority
```

A CPU-intensive process may move toward lower-priority queues, while interactive/short-running behavior may receive higher priority under the configured policy.

The exact rules vary by implementation.

---

# 45. Important Comparison

| Algorithm   | Basic Idea                        | Preemptive?            | Starvation Possible?                   |
| ----------- | --------------------------------- | ---------------------- | -------------------------------------- |
| FCFS        | First arrival runs first          | No                     | No                                     |
| SJF         | Shortest job first                | No                     | Yes                                    |
| SRTF        | Shortest remaining time           | Yes                    | Yes                                    |
| Round Robin | Fixed time quantum                | Yes                    | Generally designed to avoid starvation |
| Priority    | Highest-priority process first    | Can be either          | Yes                                    |
| HRRN        | Highest response ratio            | Usually non-preemptive | Reduced compared with SJF              |
| MLQ         | Separate fixed queues             | Depends on design      | Can occur                              |
| MLFQ        | Processes can move between queues | Depends on design      | Policies can be designed to reduce it  |

> The exact behavior of MLQ/MLFQ depends on the configured policies.

---

# 46. Gantt Chart

Gantt charts are extremely important for placement problems.

A Gantt chart shows which process runs during each time interval.

Example:

```text
|  P1  |  P2  |  P3  |
0      5      8      10
```

This means:

```text
P1 runs from 0 → 5
P2 runs from 5 → 8
P3 runs from 8 → 10
```

From this, we can calculate:

* Completion Time
* Turnaround Time
* Waiting Time
* Response Time

---

# 47. Placement Problem — FCFS

Given:

| Process | AT | BT |
| ------- | -: | -: |
| P1      |  0 |  5 |
| P2      |  1 |  3 |
| P3      |  2 |  2 |

FCFS order:

```text
P1 → P2 → P3
```

Gantt chart:

```text
|   P1   | P2 | P3 |
0        5    8    10
```

Completion times:

```text
P1 = 5
P2 = 8
P3 = 10
```

Turnaround:

```text
P1 = 5 - 0 = 5
P2 = 8 - 1 = 7
P3 = 10 - 2 = 8
```

Waiting:

```text
P1 = 5 - 5 = 0
P2 = 7 - 3 = 4
P3 = 8 - 2 = 6
```

Average waiting time:

```text
(0 + 4 + 6) / 3

= 10 / 3

≈ 3.33 ms
```

---

# 48. How to Solve Scheduling Problems

For almost every placement scheduling question, follow this order.

### Step 1 — Write the table

```text
Process | Arrival | Burst | Priority
```

Only include columns required by the question.

---

### Step 2 — Identify the algorithm

Ask:

```text
FCFS?
SJF?
SRTF?
Round Robin?
Priority?
HRRN?
```

---

### Step 3 — Draw the Gantt Chart

Example:

```text
| P1 | P2 | P3 |
0    5    8    10
```

---

### Step 4 — Find Completion Time

Look at where each process finishes.

---

### Step 5 — Calculate Turnaround Time

```text
TAT = CT - AT
```

---

### Step 6 — Calculate Waiting Time

```text
WT = TAT - BT
```

---

### Step 7 — Calculate the Average

For example:

```text
Average WT
= (WT1 + WT2 + WT3) / Number of Processes
```

---

# 49. Important Placement Trick

If you are given:

```text
Arrival Time
Burst Time
```

and asked for:

> **Average Waiting Time**

Your general flow should be:

```text
AT + BT
   ↓
Apply scheduling algorithm
   ↓
Gantt Chart
   ↓
Completion Time
   ↓
Turnaround Time
   ↓
Waiting Time
   ↓
Average
```

Do not try to directly guess the answer.

---

# 50. Placement Question — SRTF

Consider:

| Process | Arrival Time | Burst Time |
| ------- | -----------: | ---------: |
| P0      |            0 |          9 |
| P1      |            1 |          4 |
| P2      |            2 |          9 |

SRTF is used.

### Time 0

Only P0 exists.

```text
P0 → Running
```

At time 1:

```text
P0 remaining = 8
P1 burst = 4
```

P1 is shorter.

So:

```text
P0 → Preempted
P1 → Running
```

P1 completes at:

```text
1 + 4 = 5
```

At time 2, P2 arrives, but P1 remains the shortest.

Then compare P0 and P2:

```text
P0 remaining = 8
P2 = 9
```

So P0 runs next.

A possible Gantt chart:

```text
| P0 |   P1   |  P0  |   P2   |
0    1        5      13       22
```

The important placement lesson is:

> **In SRTF, always compare remaining burst time, not original burst time.**

---

# 51. Another Placement Question

Consider:

| Process | AT | BT |
| ------- | -: | -: |
| P1      |  0 |  5 |
| P2      |  1 |  3 |
| P3      |  2 |  3 |
| P4      |  4 |  1 |

Using SRTF, one execution order is:

```text
P1 → P2 → P4 → P3 → P1
```

Gantt chart:

```text
| P1 |  P2  | P4 |  P3  | P1 |
0    1      4    5      8    12
```

Completion times:

```text
P1 = 12
P2 = 4
P3 = 8
P4 = 5
```

Turnaround:

```text
P1 = 12 - 0 = 12
P2 = 4 - 1 = 3
P3 = 8 - 2 = 6
P4 = 5 - 4 = 1
```

Average:

```text
(12 + 3 + 6 + 1) / 4

= 22 / 4

= 5.5 ms
```

---

# 52. Placement Question — SRTF Waiting Time

Suppose:

| Process | Arrival Time | Burst Time |
| ------- | -----------: | ---------: |
| P1      |            0 |         20 |
| P2      |           15 |         25 |
| P3      |           30 |         10 |
| P4      |           45 |         15 |

Under SRTF:

At:

```text
t = 0
```

P1 runs.

At:

```text
t = 15
```

P2 arrives.

P1 has:

```text
20 - 15 = 5 ms remaining
```

P1 remains shorter than P2:

```text
5 < 25
```

So P1 continues and finishes at:

```text
t = 20
```

Then P2 runs.

At t = 30:

```text
P2 has remaining time
P3 arrives with 10 ms
```

P3 becomes the shortest and runs.

Eventually P2 finishes at:

```text
t = 55
```

Waiting time for P2:

```text
WT = CT - AT - BT

   = 55 - 15 - 25

   = 15 ms
```

This formula is especially useful for scheduling problems:

```text
WT = CT - AT - BT
```

---

# 53. Common Placement Traps

## Trap 1: Confusing Burst Time and Waiting Time

Burst time:

> CPU time required.

Waiting time:

> Time waiting for CPU.

They are not the same.

---

## Trap 2: Forgetting Arrival Time

Never assume every process arrives at time 0 unless the question says so.

---

## Trap 3: Using Original Burst Time in SRTF

Wrong:

```text
Compare original BT
```

Correct:

```text
Compare remaining BT
```

---

## Trap 4: Forgetting Preemption

In SRTF and preemptive Priority Scheduling, a newly arrived process may interrupt the currently running process depending on the scheduling rule.

---

## Trap 5: Confusing Response Time and Waiting Time

Response Time:

```text
Time until first CPU service
```

Waiting Time:

```text
Total time waiting in the ready queue
```

---

# 54. Most Important Algorithms to Master for Placements

If you are preparing for technical interviews, learn them in this order:

```text
                 CPU Scheduling
                       |
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
     FCFS             SJF          Round Robin
                       |
                      SRTF
                       |
                 Priority
                       |
             ┌─────────┴─────────┐
             ↓                   ↓
            HRRN              MLQ/MLFQ
```

Start with:

1. FCFS
2. SJF
3. SRTF
4. Round Robin
5. Priority Scheduling

Then move to:

6. HRRN
7. Multilevel Queue
8. Multilevel Feedback Queue

---

# 55. Technical Explanation

Now let's move from the simple explanation to the technical definition.

> **CPU scheduling is the OS mechanism that selects a runnable process/thread from the ready-to-run set and allocates CPU execution to it according to a scheduling policy.**

The scheduler attempts to optimize one or more objectives such as:

* CPU utilization
* Throughput
* Turnaround time
* Waiting time
* Response time
* Fairness

The process state is typically represented conceptually as:

```text
New
 ↓
Ready ↔ Running
 ↓       ↓
Waiting  ↓
 ↓       ↓
 └────→ Ready
          ↓
      Terminated
```

A **preemptive scheduler** can interrupt a running task and select another runnable task.

A **non-preemptive scheduler** generally allows the current task to continue until it terminates or blocks.

---

# 56. Technical View of a Context Switch

Suppose:

```text
CPU → Process P1
```

The OS decides to run P2.

Conceptually:

```text
1. Save P1 execution context
2. Update P1's process/thread state
3. Select P2
4. Restore P2 execution context
5. Resume P2
```

The saved execution context can include:

```text
Program Counter
CPU Registers
Stack Pointer
Processor Status
Architecture-specific state
```

Context switching introduces overhead, so scheduling policies must balance responsiveness against switching costs.

---

# 57. Technical View of Scheduling Metrics

For process `Pi`:

```text
Turnaround Time:

TATi = CTi - ATi
```

Waiting time:

```text
WTi = TATi - BTi
```

Therefore:

```text
WTi = CTi - ATi - BTi
```

Response time:

```text
RTi = First Start Timei - ATi
```

Average waiting time:

```text
Average WT = ΣWT / Number of Processes
```

Average turnaround time:

```text
Average TAT = ΣTAT / Number of Processes
```

---

# 58. Interview Questions

## Basic Level

### 1. What is CPU scheduling?

> CPU scheduling is the process by which the OS selects a runnable process/thread to execute on the CPU.

### 2. What is a ready queue?

> A queue or collection containing processes/threads that are ready to execute but are waiting for CPU service.

### 3. What is burst time?

> The CPU execution time required by a process, as specified by the scheduling problem.

### 4. What is arrival time?

> The time at which a process enters the ready queue.

### 5. What is turnaround time?

```text
TAT = CT - AT
```

### 6. What is waiting time?

```text
WT = TAT - BT
```

### 7. What is response time?

> Time from arrival until the process first receives CPU service.

---

# 59. Intermediate Interview Questions

### 8. Difference between SJF and SRTF?

```text
SJF
→ Non-preemptive
→ Shortest burst first

SRTF
→ Preemptive
→ Shortest remaining time first
```

---

### 9. Which scheduling algorithm can cause starvation?

Possible examples include:

```text
SJF
SRTF
Priority Scheduling
```

depending on the workload and scheduling policy.

---

### 10. How can starvation be reduced?

One common technique is:

```text
Aging
```

A process that waits for a long time can gradually receive higher scheduling priority.

---

### 11. Why is Round Robin useful?

Because it gives each runnable process a time slice, making it suitable for time-sharing/interactive workloads.

---

### 12. What happens if the Round Robin time quantum is too small?

There can be many context switches, increasing overhead.

---

### 13. What happens if the Round Robin time quantum is very large?

Its behavior approaches FCFS for many workloads.

---

### 14. Which algorithm gives minimum average waiting time?

For the standard theoretical SJF problem where burst times are known and under the usual assumptions:

> **SJF minimizes average waiting time.**

Its preemptive counterpart, SRTF, has the analogous optimality result under corresponding assumptions.

---

# 60. Rapid Revision Sheet

```text
CPU Scheduling
= Choosing which runnable process gets CPU next.

Arrival Time
= When process enters ready queue.

Burst Time
= CPU time required.

Completion Time
= When process finishes.

Turnaround Time
= CT - AT

Waiting Time
= TAT - BT
= CT - AT - BT

Response Time
= First Start Time - AT

FCFS
= First arrival first.

SJF
= Shortest job first.
= Non-preemptive.
= Minimum average waiting time under standard assumptions.
= Starvation possible.

SRTF
= Shortest remaining time first.
= Preemptive SJF.
= Compare remaining burst time.

Round Robin
= Fixed time quantum.
= Preemptive.
= Fair time-sharing approach.
= Very small quantum → more context-switch overhead.

Priority
= Highest-priority process first.
= Can be preemptive or non-preemptive.
= Starvation possible.

HRRN
= Highest response ratio next.
= Uses waiting time + burst time.

MLQ
= Multiple fixed queues.

MLFQ
= Processes can move between queues according to policy.

Gantt Chart
= Shows execution order over time.
```

---

# 61. The Most Important Mental Model

Whenever you see a CPU scheduling problem, think:

```text
             PROCESSES
                 |
                 ↓
           READY QUEUE
                 |
                 ↓
             SCHEDULER
                 |
                 ↓
                CPU
                 |
       ┌─────────┴─────────┐
       ↓                   ↓
    FINISH                 I/O
       ↓                   ↓
 TERMINATED              WAITING
                           |
                       I/O complete
                           |
                           ↓
                         READY
```

Then ask:

```text
1. Which process arrives first?
2. Which process should run according to the algorithm?
3. Can the current process be preempted?
4. What is the Gantt chart?
5. What is each process's completion time?
6. What is its turnaround time?
7. What is its waiting time?
8. What is its response time?
9. What is the average?
```

If you can answer those **9 questions**, you can solve most basic-to-intermediate CPU scheduling placement problems.

---

# 62. Final Placement Strategy

Don't try to memorize every algorithm immediately.

Master the concepts in this sequence:

```text
Process
   ↓
Process States
   ↓
Ready Queue
   ↓
CPU Scheduling
   ↓
Scheduling Metrics
   ↓
Gantt Charts
   ↓
FCFS
   ↓
SJF
   ↓
SRTF
   ↓
Round Robin
   ↓
Priority Scheduling
   ↓
Starvation & Aging
   ↓
HRRN
   ↓
MLQ
   ↓
MLFQ
```

Then practice numerical problems until you can construct a Gantt chart without hesitation.

> **Placement golden rule:** Don't memorize the final answer of a scheduling problem. Learn how to construct the **Gantt chart**. Once the Gantt chart is correct, `CT`, `TAT`, `WT`, and `RT` become straightforward calculations.
