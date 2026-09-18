# 📞 System Call in Operating System

A **System Call** is the mechanism through which a user-level program requests a service from the **Operating System kernel**.

Applications cannot freely access hardware or protected OS resources. Instead, they use system calls as a **controlled gateway between User Mode and Kernel Mode**.

```text
┌──────────────────────────┐
│      Application         │
│   Chrome / C / Python    │
└────────────┬─────────────┘
             ↓
        System Call
             ↓
┌──────────────────────────┐
│          Kernel          │
│   OS Resource Manager    │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│         Hardware         │
│ CPU │ RAM │ Disk │ I/O   │
└──────────────────────────┘
```

---

## 📌 Table of Contents

* [1. What is a System Call?](#1-what-is-a-system-call)
* [2. Why Do We Need System Calls?](#2-why-do-we-need-system-calls)
* [3. User Mode vs Kernel Mode](#3-user-mode-vs-kernel-mode)
* [4. How Does a System Call Work?](#4-how-does-a-system-call-work)
* [5. Common Examples](#5-common-examples)
* [6. Types of System Calls](#6-types-of-system-calls)
  * [6.1 File System](#61-file-system)
  * [6.2 Process Control](#62-process-control)
  * [6.3 Memory Management](#63-memory-management)
  * [6.4 Inter-Process Communication (IPC)](#64-inter-process-communication-ipc)
  * [6.5 Device Management](#65-device-management)
* [7. System Call vs Function Call](#7-system-call-vs-function-call)
* [8. System Call vs Context Switch](#8-system-call-vs-context-switch)
* [9. Technical Explanation](#9-technical-explanation)
* [10. System Call Interface](#10-system-call-interface)
* [11. Key Points to Remember](#11-key-points-to-remember)
* [12. Placement Questions](#12-placement-questions)
* [13. Quick Revision](#13-quick-revision)
* [14. One-Minute Interview Answer](#14-one-minute-interview-answer)
* [15. Next Topics](#15-next-topics)

---

# 1. What is a System Call?

Think of a system call as a **request counter**.

You are inside a building and want something from a restricted room.

You cannot enter the room directly. Instead:

```text
You
 ↓
Request
 ↓
Authorized Manager
 ↓
Resource
```

Similarly:

```text
Application
     ↓
System Call
     ↓
Kernel
     ↓
OS Resource
```

### Example

Suppose a C program wants to read a file.

The application requests:

```text
"Please read this file."
```

The kernel checks and performs the operation.

```text
Program
   ↓
read()
   ↓
Kernel
   ↓
File System
   ↓
Disk
   ↓
Data
```

---

# 2. Why Do We Need System Calls?

Applications should not have unrestricted access to hardware.

Imagine if every application could directly control:

```text
CPU
RAM
Disk
Keyboard
Network
```

A malicious or buggy application could:

* Modify another application's memory
* Access protected files
* Damage system resources
* Make the system unstable

Therefore:

> **System calls provide controlled access to OS services.**

---

# 3. User Mode vs Kernel Mode

System calls are closely related to **User Mode** and **Kernel Mode**.

### User Mode

Normal applications run here.

Examples:

```text
Chrome
VS Code
Games
C Programs
Python Programs
```

They have restricted privileges.

### Kernel Mode

The kernel runs here with privileged access to protected resources.

```text
User Mode
    ↓
System Call
    ↓
Kernel Mode
    ↓
Perform Operation
    ↓
User Mode
```

---

# 4. How Does a System Call Work?

A simplified sequence is:

```text
1. Application requests an OS service
              ↓
2. System call instruction is executed
              ↓
3. CPU enters kernel mode
              ↓
4. Kernel identifies the requested system call
              ↓
5. Kernel performs the operation
              ↓
6. Result / error is produced
              ↓
7. Execution returns to user mode
              ↓
8. Application continues
```

---

## 💻 Example: Reading a File

Suppose a program wants to read:

```text
notes.txt
```

The flow is:

```text
Application
     ↓
read()
     ↓
System Call Interface
     ↓
Kernel
     ↓
File System
     ↓
Storage Device
     ↓
Data returned
     ↓
Application
```

The application doesn't need to know the low-level details of the storage device.

---

# 5. Common Examples

## 1. Opening a File

A C program may use:

```c
fopen("notes.txt", "r");
```

The C library provides `fopen()`, which ultimately uses OS facilities such as the `open()` system call on Unix-like systems.

```text
fopen()
   ↓
Library
   ↓
open()
   ↓
Kernel
   ↓
File System
```

---

## 2. Reading Data

A program can request data from a file using operations such as:

```c
read();
```

```text
Application
     ↓
read()
     ↓
Kernel
     ↓
File
```

---

## 3. Writing Data

For example:

```c
write();
```

can be used to request a write operation.

A simple output operation can ultimately involve the `write()` system call.

---

## 4. Creating a Process

On Unix-like systems, process creation and execution can involve:

```text
fork()
exec()
```

Conceptually:

```text
Parent Process
      ↓
    fork()
      ↓
Child Process
      ↓
    exec()
      ↓
New Program
```

---

# 6. Types of System Calls

System calls can be grouped according to the services they provide.

```text
System Calls
│
├── File System
├── Process Control
├── Memory Management
├── Interprocess Communication
└── Device Management
```

---

## 6.1 File System

Used for working with files and directories.

Typical operations include:

```text
Create
Open
Read
Write
Close
Delete
```

### Example

```text
Application
     ↓
open()
     ↓
Kernel
     ↓
File System
     ↓
Storage
```

### Used for

* Creating files
* Opening files
* Reading files
* Writing files
* Managing directories

---

## 6.2 Process Control

Used to manage processes.

Operations include:

```text
Create
Execute
Terminate
Synchronize
```

Examples on Unix-like systems include:

```text
fork()
exec()
wait()
exit()
```

### Example

```text
Parent Process
      ↓
    fork()
      ↓
Child Process
      ↓
    exec()
      ↓
Execute Program
```

---

## 6.3 Memory Management

Memory-related system calls/services allow programs to request and manage memory.

The OS controls:

```text
Process
   ↓
Memory Request
   ↓
Kernel
   ↓
Memory Management
```

The kernel ensures that processes cannot arbitrarily access memory belonging to other protected processes.

---

## 6.4 Inter-Process Communication (IPC)

Processes sometimes need to communicate with each other.

The OS provides IPC mechanisms such as:

* Message passing
* Shared memory

```text
Process A
    ↓
   IPC
    ↓
Process B
```

### Example

One process may send data to another process using a communication mechanism provided by the OS.

---

## 6.5 Device Management

System calls can be used to request operations involving devices.

Examples:

```text
Keyboard
Disk
Printer
Network Device
```

Conceptually:

```text
Application
     ↓
System Call
     ↓
Kernel
     ↓
Device Driver
     ↓
Hardware
```

---

# 7. System Call vs Function Call

This is an important placement concept.

### Normal Function Call

A function call generally stays within the application's execution environment.

```text
main()
 ↓
function()
 ↓
return
```

### System Call

A system call requests a service from the kernel.

```text
Application
    ↓
System Call
    ↓
Kernel
    ↓
Return Result
```

So:

> **Every system call is a request for an OS service, but not every function call is a system call.**

---

# 8. System Call vs Context Switch

This is a **very common interview trap**.

A system call normally causes a transition:

```text
User Mode
    ↓
Kernel Mode
```

This is a **mode switch**.

It does **not automatically mean a context switch**.

A **context switch** means changing the currently executing process/thread and saving/restoring its execution state.

```text
System Call:
User Mode → Kernel Mode → User Mode

Context Switch:
Process A → Process B
```

### Important

A system call may result in a context switch **if the calling process blocks**, for example while waiting for I/O.

But:

> **System call ≠ context switch**

---

# 9. Technical Explanation

Technically, a system call is a **controlled entry point into the kernel**.

A program typically invokes a library/API function, which eventually performs the architecture-specific system call mechanism.

The CPU then transfers control to the kernel.

The kernel:

1. Identifies the requested system call.
2. Validates arguments and permissions.
3. Performs the required operation.
4. Produces a return value or error.
5. Returns execution to user space.

```text
┌───────────────────┐
│    User Space     │
│                   │
│    Application    │
└─────────┬─────────┘
          │
          │ System Call
          ↓
┌───────────────────┐
│   Kernel Space    │
│                   │
│ System Call       │
│ Handler           │
│       ↓           │
│ OS Service        │
└─────────┬─────────┘
          ↓
      Hardware
```

---

# 10. System Call Interface

The kernel maintains a mechanism for identifying different system calls.

Conceptually:

```text
System Call Number
        ↓
System Call Handler
        ↓
Requested Service
```

For example:

```text
Application
     ↓
"read"
     ↓
System Call Number
     ↓
Kernel Handler
     ↓
Read Operation
```

The exact mechanism and system-call numbers depend on the operating system and CPU architecture.

---

# 11. Key Points to Remember

```text
System Call
     ↓
Controlled gateway to kernel
     ↓
User Mode → Kernel Mode
     ↓
Kernel performs OS service
     ↓
Kernel → User Mode
```

### Main purposes

* Access files
* Create/manage processes
* Manage memory
* Communicate between processes
* Access devices

---

# 12. Placement Questions

### Q1. What is a system call?

> A system call is a controlled interface through which a user-level program requests a service from the operating system kernel.

---

### Q2. Why are system calls required?

> They provide controlled and protected access to OS services and hardware resources.

---

### Q3. What happens during a system call?

> The application invokes the system-call mechanism, execution enters kernel mode, the kernel performs the requested operation, and control returns to user mode with a result or error.

---

### Q4. Does every system call cause a context switch?

> No. A system call normally causes a **mode switch**, not necessarily a context switch. A context switch may occur if the process blocks and another process/thread is scheduled.

---

### Q5. What is the difference between user mode and kernel mode?

| User Mode                                | Kernel Mode                           |
| ---------------------------------------- | ------------------------------------- |
| Applications normally execute here       | Kernel executes here                  |
| Restricted privileges                    | Privileged operations                 |
| Cannot freely access protected resources | Can access protected system resources |
| Used for application code                | Used for OS core operations           |

---

### Q6. Give examples of system calls.

Examples on Unix-like systems include:

```text
open()
read()
write()
close()
fork()
exec()
wait()
exit()
```

---

### Q7. What are the major categories of system calls?

> File management, process control, memory management, inter-process communication, and device management.

---

### Q8. What happens if a system call fails?

> The kernel returns an error indication to the application, allowing the application to handle the failure.

---

# 13. Quick Revision

```text
┌──────────────────────────────────┐
│          SYSTEM CALL             │
└──────────────────────────────────┘

Purpose
   ↓
Request OS service

Who uses it?
   ↓
User-level programs

Where does it execute?
   ↓
Kernel handles the request

Main transition
   ↓
User Mode → Kernel Mode

Main categories
   ↓
File
Process
Memory
IPC
Device

Important distinction
   ↓
System Call ≠ Context Switch
```

---

# 14. One-Minute Interview Answer

If the interviewer asks:

> **"Explain System Calls."**

You can answer:

> A system call is a controlled interface between a user-level application and the operating system kernel. Applications use system calls when they need services such as file access, process creation, memory management, inter-process communication, or device operations. When a system call is invoked, execution enters kernel mode, the kernel validates and performs the requested operation, and then returns the result to the application. A system call normally involves a mode switch, but it does not necessarily cause a context switch.

---

# 15. Next Topics

After understanding System Calls, study:

```text
System Calls
     ↓
Processes
     ↓
Process States
     ↓
Process Control Block (PCB)
     ↓
Context Switching
     ↓
Threads
     ↓
CPU Scheduling
```

> **Placement Focus:** Make sure you can clearly explain **System Call vs Function Call**, **System Call vs Context Switch**, and **User Mode vs Kernel Mode**. These are common areas where interviewers test whether you actually understand the concept rather than memorizing the definition.
