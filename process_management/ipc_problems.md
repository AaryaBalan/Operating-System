# Classical IPC Problems in Operating Systems

## Table of Contents

1. [What Is IPC?](#1-what-is-ipc)
2. [Why Do Classical IPC Problems Matter?](#2-why-do-classical-ipc-problems-matter)
3. [Producer-Consumer Problem](#3-producer-consumer-problem)
4. [Readers-Writers Problem](#4-readers-writers-problem)
5. [Dining Philosophers Problem](#5-dining-philosophers-problem)
6. [Sleeping Barber Problem](#6-sleeping-barber-problem)
7. [Comparison of Classical IPC Problems](#7-comparison-of-classical-ipc-problems)
8. [Important Interview Questions](#8-important-interview-questions)
9. [Quick Revision](#9-quick-revision)

---

## 1. What Is IPC?

**Inter-Process Communication (IPC)** refers to mechanisms that allow processes to exchange data and coordinate their activities.

Processes may need to communicate because they share data, depend on each other's results, or use common resources.

Common IPC mechanisms include:

- Shared memory
- Message passing
- Pipes
- Message queues
- Sockets
- Semaphores and mutexes for synchronization

**Important distinction:** IPC is about communication between processes. Synchronization controls when processes access shared data or resources. Classical IPC problems commonly demonstrate the synchronization challenges that arise when processes interact.

## 2. Why Do Classical IPC Problems Matter?

When multiple processes or threads access shared resources, several problems can occur:

- **Race condition:** The result depends on the timing or order of concurrent operations.
- **Resource conflict:** Multiple processes attempt to use the same resource at the same time.
- **Deadlock:** Processes wait indefinitely for resources held by one another.
- **Starvation:** A process waits indefinitely because other processes repeatedly get access first.
- **Buffer overflow:** A producer attempts to add data to a full buffer.
- **Buffer underflow:** A consumer attempts to remove data from an empty buffer.

Classical problems provide models for understanding these issues and designing correct synchronization mechanisms.

The four major problems are:

1. Producer-Consumer Problem
2. Readers-Writers Problem
3. Dining Philosophers Problem
4. Sleeping Barber Problem

---

## 3. Producer-Consumer Problem

### 3.1 What Is the Producer-Consumer Problem?

The Producer-Consumer Problem, also called the **Bounded-Buffer Problem**, involves two types of processes:

- **Producer:** Creates data and places it into a shared buffer.
- **Consumer:** Removes data from the buffer and processes it.

The buffer has a limited capacity.

For example, imagine a queue that can hold only three items.

- The producer adds items to the queue.
- The consumer removes items from the queue.
- If the queue is full, the producer must wait.
- If the queue is empty, the consumer must wait.

<escape>Producer → Shared Buffer → Consumer</escape>

### 3.2 Main Problems

**Buffer overflow**

The producer attempts to insert an item when the buffer is already full.

**Buffer underflow**

The consumer attempts to remove an item when the buffer is empty.

**Race condition**

The producer and consumer may modify shared buffer data concurrently without proper synchronization.

### 3.3 Solution Using Semaphores

For a buffer of capacity \(N\), three synchronization variables are commonly used:

| Variable | Initial value | Purpose |
|---|---:|---|
| `empty` | N | Counts available buffer slots |
| `full` | 0 | Counts occupied buffer slots |
| `mutex` | 1 | Protects the shared buffer |

A semaphore's `wait()` operation acquires permission or blocks when permission is unavailable. Its `signal()` operation releases permission and can wake a waiting process.

#### Producer pseudocode

```text
while true:
    item = produce_item()

    wait(empty)
    wait(mutex)

    insert_item_into_buffer(item)

    signal(mutex)
    signal(full)
```

#### Consumer pseudocode

```text
while true:
    wait(full)
    wait(mutex)

    item = remove_item_from_buffer()

    signal(mutex)
    signal(empty)

    consume_item(item)
```

### 3.4 How It Works

Suppose the buffer capacity is 3.

Initially:

```text
empty = 3
full  = 0
mutex = 1
```

After the producer inserts one item:

```text
empty = 2
full  = 1
```

After the consumer removes that item:

```text
empty = 3
full  = 0
```

If `empty` reaches zero, the producer waits until a consumer frees a slot. If `full` reaches zero, the consumer waits until an item becomes available.

**Why is `mutex` necessary?**

It ensures that only one process at a time modifies the shared buffer.

### 3.5 Python Implementation

Python's `queue.Queue` provides a thread-safe bounded queue, making it convenient to demonstrate producer-consumer synchronization.

```python
from queue import Queue
from threading import Thread
from time import sleep

buffer = Queue(maxsize=3)

def producer():
    for item in range(5):
        buffer.put(item)  # Waits if the buffer is full
        print("Produced:", item)
        sleep(0.2)

def consumer():
    for _ in range(5):
        item = buffer.get()  # Waits if the buffer is empty
        print("Consumed:", item)
        buffer.task_done()
        sleep(0.5)

p = Thread(target=producer)
c = Thread(target=consumer)

p.start()
c.start()

p.join()
c.join()
```

**Note:** The exact order of the printed messages may vary because thread scheduling is nondeterministic.

### 3.6 Applications

- Task queues
- Print spooling
- Data streaming
- Logging systems
- Network packet processing

---

## 4. Readers-Writers Problem

### 4.1 What Is the Readers-Writers Problem?

The Readers-Writers Problem involves multiple processes accessing the same shared resource, such as a database or file.

There are two types of processes:

- **Reader:** Reads the shared data without modifying it.
- **Writer:** Modifies the shared data.

Multiple readers can safely read simultaneously, provided no writer is modifying the data. A writer requires exclusive access.

### 4.2 Main Rules

1. Multiple readers may read concurrently.
2. Only one writer may write at a time.
3. Readers must not read while a writer is modifying the shared resource.
4. The synchronization policy should consider whether readers or writers receive preference.

### 4.3 Readers-Preference Solution

In the readers-preference approach, readers are allowed to enter together as long as no writer is currently writing.

The first reader blocks writers. The last reader allows writers to proceed.

Common synchronization variables:

- `read_count`: Number of active readers.
- `mutex`: Protects `read_count`.
- `rw_mutex`: Controls access to the shared resource.

#### Pseudocode

```text
Reader:
    wait(mutex)
    read_count = read_count + 1

    if read_count == 1:
        wait(rw_mutex)

    signal(mutex)

    read_shared_data()

    wait(mutex)
    read_count = read_count - 1

    if read_count == 0:
        signal(rw_mutex)

    signal(mutex)
```

```text
Writer:
    wait(rw_mutex)

    write_shared_data()

    signal(rw_mutex)
```

#### How it works

- The first reader acquires exclusive control over the resource against writers.
- Additional readers can join without blocking one another.
- The writer waits until all readers have finished.
- The last reader releases the resource for writers.

**Limitation:** If readers keep arriving, a writer may wait indefinitely. This is called writer starvation.

### 4.4 Writers-Preference Solution

In the writers-preference approach, waiting writers are given preference over newly arriving readers.

When a writer is waiting, new readers are prevented from entering until the waiting writer gets an opportunity to write.

**Advantage:** Reduces the risk of writer starvation.

**Limitation:** If writers keep arriving, readers may experience starvation.

### 4.5 Fair Readers-Writers Solution

A fair solution attempts to serve readers and writers in an order that prevents either group from being indefinitely postponed.

One common design uses a queue or turnstile so that new arrivals cannot continuously bypass waiting processes.

Fairness depends on the implementation and the scheduling guarantees of the synchronization primitives.

### 4.6 Python Implementation

This example demonstrates the readers-preference approach using a condition variable.

```python
from threading import Condition, Thread
from time import sleep

condition = Condition()
read_count = 0
writer_active = False
shared_data = 0

def reader(reader_id):
    global read_count

    with condition:
        while writer_active:
            condition.wait()

        read_count += 1

    # Multiple readers may be here simultaneously.
    print(f"Reader {reader_id} reads:", shared_data)
    sleep(0.2)

    with condition:
        read_count -= 1

        if read_count == 0:
            condition.notify_all()

def writer(writer_id, value):
    global writer_active, shared_data

    with condition:
        while writer_active or read_count > 0:
            condition.wait()

        writer_active = True

    try:
        print(f"Writer {writer_id} is writing")
        shared_data = value
        sleep(0.2)
    finally:
        with condition:
            writer_active = False
            condition.notify_all()

threads = [
    Thread(target=reader, args=(1,)),
    Thread(target=reader, args=(2,)),
    Thread(target=writer, args=(1, 100)),
    Thread(target=reader, args=(3,))
]

for thread in threads:
    thread.start()

for thread in threads:
    thread.join()
```

This illustrates the core idea, but it is not a fully fair readers-writers lock. New readers can sometimes enter ahead of a waiting writer, and writer starvation is possible. Also, the shared value is read in the demonstration without holding the condition lock; a production implementation should protect the actual read operation against concurrent writes.

### 4.7 Applications

- Database systems
- File systems
- Shared configuration data
- Caches
- Systems with frequent reads and occasional updates

---

## 5. Dining Philosophers Problem

### 5.1 What Is the Dining Philosophers Problem?

The Dining Philosophers Problem models several processes competing for shared resources.

Imagine five philosophers sitting around a circular table. There is one chopstick between each pair of neighboring philosophers.

Each philosopher alternates between:

- Thinking
- Eating

To eat, a philosopher needs both the left and right chopsticks.

Each chopstick can be held by only one philosopher at a time.

### 5.2 Main Problems

**Deadlock**

Suppose every philosopher picks up their left chopstick simultaneously. Each philosopher then waits for the right chopstick, which is held by a neighbor.

Nobody can proceed, so the system is deadlocked.

**Starvation**

One philosopher may repeatedly lose the opportunity to acquire both chopsticks while other philosophers continue eating.

**Resource contention**

Neighboring philosophers compete for shared chopsticks.

### 5.3 Solution 1: Resource Ordering

Assign each chopstick a unique number. Every philosopher must acquire the lower-numbered chopstick first and the higher-numbered chopstick second.

This breaks the circular-wait condition and therefore prevents deadlock.

However, resource ordering alone does not guarantee that every philosopher will eventually eat; starvation depends on scheduling and lock fairness.

### 5.4 Solution 2: Semaphore

Use one binary semaphore per chopstick.

```text
for each philosopher i:
    wait(chopstick[left])
    wait(chopstick[right])

    eat()

    signal(chopstick[right])
    signal(chopstick[left])
```

**Important:** This pseudocode alone does not prevent deadlock. If all philosophers acquire their first chopstick before attempting their second, circular waiting is still possible.

A deadlock-prevention strategy must be added, such as resource ordering or a restriction on how many philosophers may attempt to acquire chopsticks simultaneously.

### 5.5 Solution 3: Limit Concurrent Attempts

Allow at most four of the five philosophers to attempt to acquire chopsticks simultaneously.

This can prevent the classic all-five-holding-one-chopstick deadlock, provided the entry restriction is correctly synchronized.

It does not automatically guarantee starvation freedom.

### 5.6 Python Implementation Using Resource Ordering

```python
from threading import Lock, Thread
from time import sleep

N = 5
chopsticks = [Lock() for _ in range(N)]

def philosopher(i):
    left = i
    right = (i + 1) % N

    # Always acquire the lower-numbered lock first.
    first, second = sorted((left, right))

    for _ in range(3):
        print(f"Philosopher {i} is thinking")
        sleep(0.1)

        with chopsticks[first]:
            with chopsticks[second]:
                print(f"Philosopher {i} is eating")
                sleep(0.1)

threads = [
    Thread(target=philosopher, args=(i,))
    for i in range(N)
]

for thread in threads:
    thread.start()

for thread in threads:
    thread.join()
```

Because all philosophers acquire chopsticks in the same global order, this implementation avoids circular waiting and therefore prevents the classic deadlock.

It does not guarantee fairness or eliminate starvation in every possible scheduling environment.

### 5.7 Applications

- Resource allocation in operating systems
- Database locking
- Multi-threaded applications
- Distributed resource coordination
- Systems where multiple tasks compete for several locks

---

## 6. Sleeping Barber Problem

### 6.1 What Is the Sleeping Barber Problem?

The Sleeping Barber Problem models synchronization in a service system.

Imagine a barber shop with:

- One barber
- One barber chair
- A limited number of waiting chairs
- Multiple customers

The barber sleeps when there are no customers. When a customer arrives, the customer wakes the barber if necessary.

If the barber is busy, a customer waits if a chair is available. If all waiting chairs are occupied, the customer leaves.

### 6.2 Main Rules

1. The barber sleeps when no customers are waiting.
2. An arriving customer wakes the barber if the barber is sleeping.
3. If the barber is busy and a waiting chair is available, the customer waits.
4. If no waiting chair is available, the customer leaves.
5. Synchronization must prevent lost wake-ups and incorrect accounting of waiting customers.

### 6.3 Synchronization Variables

A semaphore-based solution commonly uses:

| Variable | Purpose |
|---|---|
| `customers` | Counts customers waiting for service |
| `barber` | Signals that the barber is ready to serve a customer |
| `mutex` | Protects the number of waiting customers |
| `waiting` | Tracks the number of customers in the waiting area |
| `CHAIRS` | Maximum number of waiting chairs |

### 6.4 Pseudocode

#### Barber

```text
while true:
    wait(customers)

    wait(mutex)
    waiting = waiting - 1
    signal(barber)
    signal(mutex)

    cut_hair()
```

#### Customer

```text
wait(mutex)

if waiting < CHAIRS:
    waiting = waiting + 1
    signal(customers)
    signal(mutex)

    wait(barber)
    get_haircut()
else:
    signal(mutex)
    leave_shop()
```

The barber sleeps when `customers` has no available signal. A customer signals `customers` when joining the waiting queue.

This is a classic semaphore-based model. A complete real-world implementation also needs to account for service completion, customer-barber pairing, shutdown, and the possibility of customers arriving at different times.

### 6.5 Python Implementation

The following simplified version uses a condition variable and a bounded waiting queue. The queue handles waiting customers, and the condition variable lets the barber sleep when no customers are available.

```python
from collections import deque
from threading import Condition, Thread
from time import sleep

CHAIRS = 3
condition = Condition()
waiting_customers = deque()
shop_open = True

def barber():
    while True:
        with condition:
            while not waiting_customers and shop_open:
                print("Barber is sleeping")
                condition.wait()

            if not waiting_customers and not shop_open:
                return

            customer = waiting_customers.popleft()

        print(f"Barber is serving customer {customer}")
        sleep(0.3)
        print(f"Customer {customer} is finished")

def customer(customer_id):
    with condition:
        if not shop_open:
            print(f"Customer {customer_id} leaves: shop is closed")
            return

        if len(waiting_customers) >= CHAIRS:
            print(f"Customer {customer_id} leaves: no waiting chair")
            return

        waiting_customers.append(customer_id)
        condition.notify()

barber_thread = Thread(target=barber)
barber_thread.start()

for customer_id in range(1, 8):
    customer(customer_id)
    sleep(0.05)

with condition:
    shop_open = False
    condition.notify_all()

barber_thread.join()
```

This is a simplified teaching model. It tracks customers in the waiting area and shuts down cleanly, but it does not model each customer's blocking until their own haircut finishes.

### 6.6 Applications

- CPU or job scheduling queues
- Service request handling
- Thread pools
- Network servers
- Systems that must manage limited waiting capacity

---

## 7. Comparison of Classical IPC Problems

| Problem | Main resources | Main challenge | Common solution |
|---|---|---|---|
| Producer-Consumer | Shared buffer | Overflow, underflow, race conditions | Semaphores, mutexes, bounded queues |
| Readers-Writers | Shared data | Coordinating readers and writers | Read-write locks, semaphores, monitors |
| Dining Philosophers | Multiple shared resources | Deadlock and starvation | Resource ordering, monitors, semaphores with a prevention strategy |
| Sleeping Barber | Service capacity and waiting queue | Lost wake-ups, queue limits, coordination | Semaphores, mutexes, condition variables |

## 8. Important Interview Questions

### Q1. What is the Producer-Consumer Problem?

It is a synchronization problem in which producers insert items into a shared bounded buffer and consumers remove them. Synchronization prevents overflow, underflow, and race conditions.

### Q2. Why are `empty`, `full`, and `mutex` used?

- `empty` counts available buffer slots.
- `full` counts occupied slots.
- `mutex` protects the shared buffer during modifications.

### Q3. What is the Readers-Writers Problem?

It concerns concurrent access to shared data. Multiple readers may read together, but a writer needs exclusive access.

### Q4. What is writer starvation?

Writer starvation occurs when a writer waits indefinitely because new readers continually gain access before the writer.

### Q5. Why can the Dining Philosophers Problem cause deadlock?

If each philosopher holds one chopstick and waits for another held by a neighbor, circular waiting can occur and no philosopher can proceed.

### Q6. How can Dining Philosophers deadlock be prevented?

One approach is to enforce a global order for acquiring chopsticks. This removes circular waiting.

### Q7. What is the Sleeping Barber Problem?

It models coordination between a sleeping or busy service provider and customers competing for limited waiting space.

### Q8. What is the role of a monitor in these problems?

A monitor groups shared state and synchronized operations so that access can be coordinated safely. Condition variables allow threads to wait for specific conditions and be notified when those conditions may have changed.

### Q9. Are these problems limited to processes?

No. The classical models apply to processes and threads. Python examples often use threads because Python provides synchronization primitives such as locks, semaphores, queues, and condition variables through its threading libraries.

### Q10. What is the difference between deadlock and starvation?

Deadlock occurs when a set of tasks cannot proceed because of a blocking dependency. Starvation occurs when a task is indefinitely denied progress while other tasks may continue.

## 9. Quick Revision

- **Producer-Consumer:** Synchronizes data production and consumption through a bounded buffer.
- **Readers-Writers:** Allows concurrent reading but requires exclusive writing.
- **Dining Philosophers:** Models competing tasks that need multiple shared resources.
- **Sleeping Barber:** Models service coordination with a limited waiting queue.
- **Mutex:** Protects shared state from concurrent modification.
- **Semaphore:** Controls access through a counter or permits.
- **Monitor:** Encapsulates shared state and synchronized operations.
- **Deadlock prevention:** Removes at least one necessary condition for deadlock.
- **Starvation prevention:** Requires a policy that provides waiting tasks with a reasonable guarantee of eventual progress.

**Final takeaway:** Classical IPC problems are standard models for learning how to coordinate concurrent tasks, protect shared resources, and reason about deadlock, starvation, and fairness in operating systems.
