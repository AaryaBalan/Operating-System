# Introduction to Operating Systems

> **Placement-focused notes:** Learn every concept in this order: **simple intuition → real-world example → technical explanation → placement points**.

---

## 1. What is an Operating System?

An **Operating System (OS)** is system software that acts as a **middle layer between the user/applications and computer hardware**.

In simple terms:

> **OS = Manager of the computer**

Imagine you are in a restaurant:

* **You** → User
* **Waiter** → Operating System
* **Kitchen** → Hardware
* **Food** → Resources/services you need

You don't go directly into the kitchen and start cooking. You tell the waiter what you need, and the waiter coordinates with the kitchen.

Similarly, applications don't normally control hardware directly. They request services from the OS.

### Example

Suppose you open Chrome and play a YouTube video.

Chrome needs:

* CPU → to execute instructions
* RAM → to store running data
* Network → to receive the video
* Speakers → to play sound
* Display → to show the video

The OS coordinates access to these resources.

```text
              USER
                |
                v
        APPLICATIONS
     Chrome / VS Code / Games
                |
                v
       OPERATING SYSTEM
    +-----------------------+
    | Process Management    |
    | Memory Management     |
    | File Management       |
    | Device Management     |
    | Security              |
    +-----------------------+
                |
                v
            HARDWARE
    CPU | RAM | Disk | I/O
```

---

# 2. Why Do We Need an Operating System?

Without an OS, using a modern computer would be extremely difficult.

Imagine you want to save a file.

Without an OS, a program would need to know details such as:

* Where exactly the disk is located
* How the disk controller works
* Which physical blocks are free
* How data should be transferred
* How to communicate with the storage device

The OS hides these complicated hardware details.

Instead, you can simply do:

```text
Save file → "notes.txt"
```

The OS takes care of the underlying work.

### Simple Example

When you click:

```text
Save
```

the OS handles things such as:

```text
Application
    ↓
OS request
    ↓
File system
    ↓
Storage driver
    ↓
Disk/SSD
```

So the OS provides **abstraction**.

> **Abstraction means hiding unnecessary complexity and giving us a simpler interface.**

---

# 3. Main Goals of an Operating System

The major goals can be remembered as:

```text
Convenience
     +
Program Execution
     +
Resource Management
     +
Security
     +
Efficiency
     +
Reliability
```

---

## 3.1 User Convenience

The OS should make the computer easy to use.

For example, instead of interacting directly with hardware, you can:

```text
Click → Open File
Click → Play Music
Click → Print
```

The OS handles the complicated hardware operations behind the scenes.

### Example

You want to print a document.

You simply select:

```text
Print → Select Printer → Print
```

You don't need to manually control the printer's hardware signals.

### Placement Point

**Goal:** Make computer usage convenient and user-friendly.

---

# 4. Program Execution

One of the most important responsibilities of an OS is to provide an environment in which programs can execute.

Suppose you run:

```text
python program.py
```

The OS helps:

1. Load the program into memory.
2. Create a process.
3. Allocate resources.
4. Give CPU time to the process.
5. Handle input/output.
6. Terminate the process when it finishes.

```text
Program
   ↓
OS loads program
   ↓
Memory
   ↓
Process created
   ↓
CPU executes it
   ↓
Program finishes
```

### Placement Point

The OS provides the **environment and resources required for program execution**.

---

# 5. Resource Management

A computer has limited resources.

For example:

```text
CPU       → Limited
RAM       → Limited
Disk      → Limited
Network   → Limited
I/O       → Limited
```

Multiple programs may want these resources at the same time.

The OS acts as a **resource manager**.

### Example

Suppose you have:

```text
Chrome
VS Code
Spotify
WhatsApp
```

All of them want CPU time.

The OS decides which process gets CPU time and for how long.

```text
             CPU
              ↑
       +------+------+------+
       |      |      |      |
     Chrome VS Code Spotify ...
```

Similarly, the OS manages:

* CPU
* Main memory
* Files
* Storage
* Printers
* Keyboard
* Mouse
* Network devices
* Other I/O devices

### Placement Point

> **Operating System = Resource Manager**

This is one of the most important definitions for interviews.

---

# 6. Security

An OS must protect:

* Users
* Applications
* Files
* System resources
* Data

### Example

Suppose one application tries to access another application's private memory.

The OS should prevent unauthorized access.

Similarly, when you log into a computer:

```text
Username
   +
Password
   ↓
Authentication
   ↓
Access granted
```

The OS can also control permissions.

For example:

```text
File: salary.txt

Owner       → Read + Write
Other users → No access
```

### Important Security Concepts

You should eventually learn:

* Authentication
* Authorization
* Access control
* User permissions
* Process isolation
* Memory protection

### Placement Point

> Authentication answers **"Who are you?"**

> Authorization answers **"What are you allowed to do?"**

---

# 7. Efficient Resource Utilization

Simply managing resources is not enough.

The OS should try to use them efficiently.

For example, if the CPU is sitting idle while there are processes waiting to execute, the system is not being used efficiently.

The OS tries to improve:

* CPU utilization
* Memory utilization
* I/O utilization
* Overall throughput

### Example

Imagine a cashier in a supermarket.

If the cashier processes one customer and then waits doing nothing while another customer is ready, the system is inefficient.

Similarly, an OS tries to keep system resources productive.

### Placement Point

**Resource management** = deciding who gets resources.

**Efficient resource utilization** = using those resources effectively.

---

# 8. Reliability

The OS should continue operating correctly even when problems occur.

For example:

```text
Application crashes
       ↓
OS detects the problem
       ↓
Application terminated
       ↓
Other applications continue running
```

A crash in one application should ideally not crash the entire computer.

The OS provides mechanisms for:

* Error handling
* Process isolation
* Memory protection
* Recovery
* Exception handling

---

# 9. Components of a Computer System

A general-purpose computer can be viewed as four layers:

```text
+---------------------------+
|       Users               |
+---------------------------+
|   Application Programs    |
+---------------------------+
|     System Programs      |
+---------------------------+
|    Operating System       |
+---------------------------+
|        Hardware           |
+---------------------------+
```

Let's understand each one.

---

## 9.1 Hardware

Hardware is the physical part of the computer.

Examples:

* CPU
* RAM
* SSD/HDD
* Keyboard
* Mouse
* Monitor
* Printer
* Network card

```text
Hardware = Physical components
```

---

# 10. Operating System

The OS manages hardware and provides services to software.

Examples:

* Windows
* Linux
* macOS
* Android
* iOS
* Unix and Unix-like systems

The OS sits between applications and hardware.

```text
Application
     ↓
    OS
     ↓
 Hardware
```

---

# 11. System Programs

System programs are software that help users and developers interact with the system.

Examples include:

* Compilers
* Assemblers
* Linkers
* Loaders
* Editors
* Command interpreters
* System utilities

### Example

Suppose you write:

```c
printf("Hello");
```

A compiler converts your source code into a form that can eventually be executed by the processor.

The compiler itself is **not the kernel**.

This distinction is important.

---

# 12. Application Programs

Application programs are programs designed to perform tasks for users.

Examples:

```text
Chrome       → Web browsing
VS Code      → Programming
Spotify      → Music
Photoshop    → Image editing
Games        → Entertainment
```

Applications use OS services to access hardware.

---

# 13. Kernel

The **kernel is the core component of an operating system**.

It runs with high privileges and manages important system resources.

Think of it as the **main manager inside the OS**.

The kernel handles areas such as:

```text
Process Management
Memory Management
File System
Device Management
Security
System Calls
```

### Example

Suppose an application wants to read a file.

The application requests the OS:

```text
"I want to read notes.txt"
```

The request enters the kernel through a system call.

The kernel coordinates with the file system and storage device.

```text
Application
     ↓
System Call
     ↓
Kernel
     ↓
File System
     ↓
Storage Driver
     ↓
SSD
```

### Placement Definition

> **Kernel is the core part of an operating system that manages system resources and provides essential services to applications.**

---

# 14. Shell

The **shell** is an interface through which users can interact with the OS.

For example:

```bash
ls
```

```bash
cd Documents
```

```bash
mkdir project
```

The shell interprets these commands and requests the appropriate OS services/programs.

### Common Shells

Examples include:

* Bash
* Zsh
* PowerShell

### Important Distinction

```text
Shell ≠ Kernel
```

The shell is an interface.

The kernel is the core resource manager.

```text
User
 ↓
Shell
 ↓
System Calls
 ↓
Kernel
 ↓
Hardware
```

---

# 15. CLI and GUI

There are two common ways users interact with operating systems.

---

## 15.1 CLI — Command-Line Interface

Users type commands.

Example:

```bash
mkdir project
```

The command tells the system to create a directory.

Examples:

* Bash
* PowerShell
* Command Prompt

### Advantages

* Fast for experienced users
* Easy to automate
* Powerful for system administration
* Useful for developers

---

## 15.2 GUI — Graphical User Interface

Users interact through:

* Windows
* Icons
* Menus
* Buttons
* Mouse
* Touch

Example:

```text
Open File Manager
      ↓
Click Documents
      ↓
Double-click file
```

Examples:

* Windows desktop
* macOS Finder
* Linux desktop environments

### Important

CLI and GUI are **interfaces for interacting with the system**.

They are not the same thing as the kernel.

---

# 16. Functions of an Operating System

The major OS functions are:

1. Process Management
2. Memory Management
3. File Management
4. I/O Management
5. Storage Management
6. Security and Protection
7. Resource Allocation
8. Networking
9. Error Handling
10. Providing System Calls

Let's understand the important ones.

---

# 17. Process Management

A **program** is a passive set of instructions.

A **process** is a program that is currently executing.

Example:

```text
Chrome.exe
```

stored on disk → **Program**

When you open Chrome:

```text
Program
   ↓
Loaded into memory
   ↓
Executing
   ↓
Process
```

The OS manages processes.

It handles:

* Process creation
* Process termination
* CPU scheduling
* Process synchronization
* Inter-process communication

### Placement Question

**Q: What is the difference between a program and a process?**

**Answer:**

> A program is a passive set of instructions stored on storage, whereas a process is an active instance of a program in execution.

---

# 18. Multitasking

Modern operating systems allow multiple programs to appear to run at the same time.

For example:

```text
Chrome
VS Code
Spotify
Terminal
```

All can run concurrently.

On a single CPU core, the OS rapidly switches the CPU between processes.

```text
CPU:

Chrome → VS Code → Spotify → Chrome → VS Code
```

This switching happens very quickly.

The user experiences it as simultaneous execution.

This is called **context switching** when the CPU switches from one process/thread to another.

---

# 19. Memory Management

Programs need memory to run.

The OS manages RAM.

For example:

```text
RAM
+------------------+
| Operating System |
+------------------+
| Chrome           |
+------------------+
| VS Code          |
+------------------+
| Spotify          |
+------------------+
| Free Memory      |
+------------------+
```

The OS decides:

* Which process gets memory
* How much memory it gets
* Which memory is free
* How memory is protected

It also provides mechanisms such as:

* Virtual memory
* Paging
* Segmentation
* Memory protection

These become very important in OS interviews.

---

# 20. File Management

The OS provides a way to organize data into files and directories.

For example:

```text
Documents/
    resume.pdf
    notes.txt
    project/
        main.c
```

The OS manages:

* Creating files
* Deleting files
* Reading files
* Writing files
* File permissions
* Directories
* Storage allocation

### Example

When you execute:

```bash
cat notes.txt
```

the OS helps locate the file and retrieve its contents from storage.

---

# 21. I/O Management

I/O means:

> **Input / Output**

Examples:

### Input

```text
Keyboard
Mouse
Microphone
Camera
```

### Output

```text
Monitor
Speaker
Printer
```

The OS manages communication between applications and I/O devices.

---

# 22. Device Drivers

Hardware devices often require special software called **device drivers**.

For example:

```text
Application
     ↓
Operating System
     ↓
Printer Driver
     ↓
Printer
```

The driver helps the OS communicate with a particular hardware device.

### Simple Example

Your application doesn't need to understand every low-level detail of a printer.

It can request:

```text
Print this document
```

The OS and printer driver handle the hardware-specific details.

---

# 23. Security and Protection

The OS protects system resources from unauthorized access.

Important mechanisms include:

### Authentication

Determines who the user is.

```text
Username + Password
       ↓
Authentication
```

### Authorization

Determines what the user can access.

```text
User A
 ↓
Can read file

User B
 ↓
Cannot read file
```

### Isolation

One process should not freely access another process's memory.

---

# 24. System Calls

This is an **important placement topic**.

Applications generally cannot directly perform privileged hardware operations.

Instead, they request services from the kernel through **system calls**.

Example:

```text
Application
     ↓
System Call
     ↓
Kernel
     ↓
Hardware / OS Resource
```

For example, a program may request:

```text
open()
read()
write()
close()
```

These are examples of system-call interfaces commonly exposed by Unix-like systems.

### Simple Analogy

You want something from a restricted room.

You don't enter the room yourself.

You ask the authorized manager.

```text
Application → Kernel
```

The kernel performs the operation according to system rules.

---

# 25. User Mode and Kernel Mode

Modern CPUs provide privilege levels.

The two concepts you should know first are:

```text
User Mode
Kernel Mode
```

### User Mode

Normal applications execute here.

Examples:

```text
Chrome
VS Code
Games
```

Applications have restricted privileges.

### Kernel Mode

The kernel executes here.

It has access to privileged operations and hardware resources.

```text
User Mode
   ↓
System Call
   ↓
Kernel Mode
```

This separation improves **security and stability**.

---

# 26. Operating Systems You Should Know

## Windows

Developed by Microsoft.

Commonly used for:

* Personal computers
* Business
* Gaming
* Enterprise environments

---

## macOS

Developed by Apple.

Commonly used for:

* Personal computing
* Software development
* Creative work
* Professional environments

---

## Linux

Linux is an open-source operating-system **kernel**; Linux distributions combine the kernel with system software and other components to form complete operating systems.

Commonly used for:

* Servers
* Cloud infrastructure
* Development
* Embedded systems
* Supercomputing
* Personal computers

Examples of distributions:

```text
Ubuntu
Fedora
Debian
Arch Linux
```

### Placement Tip

Be precise:

> Linux technically refers to the kernel, while a Linux distribution is a complete operating-system environment built around that kernel.

---

# 27. Unix

Unix originated at **AT&T Bell Labs**.

Unix and Unix-like systems have historically been important in:

* Servers
* Workstations
* Research
* Academia
* Development

Linux is **Unix-like**, but Linux is not the original Unix operating system.

---

# 28. Operating System Architecture — Big Picture

A useful mental model is:

```text
+--------------------------------+
|            USER                |
+--------------------------------+
                |
                v
+--------------------------------+
|      APPLICATION PROGRAMS      |
| Chrome | VS Code | Games       |
+--------------------------------+
                |
                v
+--------------------------------+
|       SYSTEM PROGRAMS          |
| Shell | Compiler | Utilities   |
+--------------------------------+
                |
                v
+--------------------------------+
|            KERNEL              |
|                                |
| Process Management             |
| Memory Management              |
| File Management                |
| Device Management              |
| Security                       |
+--------------------------------+
                |
                v
+--------------------------------+
|           HARDWARE             |
| CPU | RAM | SSD | I/O Devices  |
+--------------------------------+
```

This diagram is worth remembering for interviews.

---

# 29. Example: Opening a File

Suppose you double-click:

```text
resume.pdf
```

What happens conceptually?

```text
User
 ↓
File Manager / Application
 ↓
OS request
 ↓
System Call
 ↓
Kernel
 ↓
File System
 ↓
Storage Driver
 ↓
SSD
 ↓
Data returned
 ↓
Application
 ↓
File displayed
```

A lot of work happens behind one simple click.

This is one of the main purposes of an OS:

> **Hide hardware complexity and provide convenient abstractions and services.**

---

# 30. Example: Playing a Song

Suppose Spotify is playing music.

The OS helps coordinate:

```text
Spotify
   ↓
CPU ← Process Management
   ↓
RAM ← Memory Management
   ↓
Network ← I/O Management
   ↓
Audio Driver
   ↓
Speaker
```

At the same time, you may have:

```text
Chrome
VS Code
Terminal
```

running.

The OS coordinates all these activities.

---

# 31. Example: Running a Program

Suppose you run:

```c
int main() {
    printf("Hello");
}
```

Conceptually:

```text
Source Code
     ↓
Compiler
     ↓
Executable
     ↓
OS loads executable
     ↓
Process created
     ↓
Memory allocated
     ↓
CPU executes instructions
     ↓
Output generated
```

The OS participates in many stages of program execution.

---

# 32. Program vs Process

This is a **very common placement question**.

| Program                       | Process                          |
| ----------------------------- | -------------------------------- |
| Passive                       | Active                           |
| Set of instructions           | Program currently executing      |
| Stored on disk/storage        | Exists in memory while executing |
| Does not have execution state | Has execution state              |
| Example: `calculator.exe`     | Running calculator               |

### Easy way to remember

```text
Program = Recipe
Process = Someone currently cooking using the recipe
```

---

# 33. Resource Management Example

Suppose:

```text
RAM = 8 GB
```

and multiple applications request memory.

```text
Chrome       → Memory
VS Code      → Memory
Spotify      → Memory
Game         → Memory
```

The OS tracks memory usage and allocates memory to processes.

The same concept applies to CPU:

```text
Chrome
   ↓
VS Code
   ↓
Game
   ↓
Spotify
```

The OS scheduler determines which runnable thread/process gets CPU time according to the scheduling policy.

---

# 34. Factors to Consider When Choosing an OS

When choosing an OS, consider:

## 34.1 Cost

Some operating systems or distributions are free, while others may involve licensing or hardware costs.

---

## 34.2 Ease of Use

A beginner may prefer a system with a familiar graphical interface.

An experienced developer may prefer a command-line-heavy environment.

---

## 34.3 Compatibility

Check whether the OS supports the:

* Applications you need
* Hardware you own
* Development tools you use
* Games/software you require

---

## 34.4 Security

Consider:

* Security updates
* Access controls
* Application isolation
* Encryption support
* User permissions

Security also depends heavily on configuration and how the system is used.

---

# 35. Important OS Terms for Placements

You should become comfortable with these terms:

```text
Operating System
Kernel
Shell
Process
Thread
Program
System Call
User Mode
Kernel Mode
CPU Scheduling
Context Switching
Memory Management
Virtual Memory
Paging
File System
Deadlock
Synchronization
Mutex
Semaphore
Inter-Process Communication
Protection
Security
Device Driver
Interrupt
```

These form the foundation of most Operating Systems interview preparation.

---

# 36. Placement Questions You Should Prepare

### Beginner Level

**1. What is an operating system?**

> An operating system is system software that manages computer hardware and provides services and an execution environment for application programs.

---

**2. Why do we need an OS?**

> To provide convenient program execution, manage hardware resources, provide abstractions, and provide protection and security.

---

**3. What is a kernel?**

> The kernel is the core component of an operating system responsible for managing resources and providing essential services to software.

---

**4. What is a shell?**

> A shell is a command interpreter/interface that allows users to interact with the operating system.

---

**5. Is shell the same as kernel?**

> No. The shell provides a user interface, while the kernel is the core component that manages system resources.

---

**6. What is a process?**

> A process is a program in execution.

---

**7. What is multitasking?**

> Multitasking is the ability of an operating system to manage multiple executing tasks so that they can make progress concurrently.

---

**8. What is a system call?**

> A system call is a controlled interface through which a user-space program requests a service from the operating system kernel.

---

# 37. Frequently Asked Interview Comparisons

These comparisons are particularly important:

```text
Program vs Process
Process vs Thread
Process vs Program
User Mode vs Kernel Mode
CLI vs GUI
Kernel vs Shell
Paging vs Segmentation
Process vs Thread
Concurrency vs Parallelism
Mutex vs Semaphore
Deadlock vs Starvation
RAM vs Virtual Memory
Logical Address vs Physical Address
Internal vs External Fragmentation
Preemptive vs Non-preemptive Scheduling
```

Don't just memorize definitions. Understand **why** each distinction exists.

---

# 38. A Complete Mental Model

If an interviewer asks:

> **"Explain the operating system."**

You can build your answer like this:

```text
Operating System
       |
       +-- Provides interface
       |
       +-- Runs/manages programs
       |
       +-- Manages CPU
       |
       +-- Manages Memory
       |
       +-- Manages Files
       |
       +-- Manages I/O Devices
       |
       +-- Provides Security
       |
       +-- Provides System Calls
       |
       +-- Protects processes/resources
```

Then explain the kernel:

```text
                    OS
                     |
             +-------+-------+
             |               |
           Shell           Kernel
                             |
                +------------+------------+
                |            |            |
             Process       Memory        I/O
             Manager       Manager      Manager
                |            |            |
                +------------+------------+
                             |
                          Hardware
```

---

# 39. One-Line Revision Notes

| Topic                 | Remember                                        |
| --------------------- | ----------------------------------------------- |
| **OS**                | Manager between applications/users and hardware |
| **Kernel**            | Core of the OS                                  |
| **Shell**             | Interface/command interpreter                   |
| **Process**           | Program in execution                            |
| **CPU Management**    | Decides which runnable work gets CPU time       |
| **Memory Management** | Allocates and protects memory                   |
| **File Management**   | Organizes and manages files/directories         |
| **I/O Management**    | Manages communication with devices              |
| **System Call**       | Interface for requesting kernel services        |
| **User Mode**         | Restricted application execution                |
| **Kernel Mode**       | Privileged kernel execution                     |
| **Multitasking**      | Managing multiple tasks concurrently            |
| **Security**          | Protecting resources/data                       |
| **Driver**            | Software enabling OS/device interaction         |
| **Resource Manager**  | OS allocates CPU, memory, I/O, etc.             |
| **Abstraction**       | Hides low-level hardware complexity             |

---

# 40. What to Study Next for Placements

This introduction gives you the foundation. For **OS placement preparation**, I recommend studying in this order:

```text
01. Introduction to OS
        ↓
02. System Calls
        ↓
03. Processes
        ↓
04. Threads
        ↓
05. Process Scheduling
        ↓
06. Context Switching
        ↓
07. Process Synchronization
        ↓
08. Critical Section
        ↓
09. Mutex & Semaphore
        ↓
10. Deadlocks
        ↓
11. Memory Management
        ↓
12. Paging
        ↓
13. Segmentation
        ↓
14. Virtual Memory
        ↓
15. Page Replacement Algorithms
        ↓
16. File Systems
        ↓
17. Disk Scheduling
        ↓
18. I/O Systems
        ↓
19. Protection & Security
        ↓
20. Important OS Interview Problems
```

### Most important for placements

If your preparation time is limited, prioritize:

**Processes → Threads → CPU Scheduling → Synchronization → Deadlocks → Memory Management → Paging → Virtual Memory → File Systems → System Calls**

These topics connect together and are much more useful than memorizing isolated definitions.