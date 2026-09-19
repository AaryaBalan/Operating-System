# What Happens When We Turn On a Computer?

> The moment you press the power button, the computer goes through a sequence of steps to check its hardware, find the operating system, load the kernel, start system services, and finally show you the login screen or desktop.
>
> This complete sequence is called the **Boot Process**.

---

## 1. What is the Boot Process?

Think of starting a computer like **opening a shop in the morning**.

Before the shop can serve customers:

1. Electricity must be available.
2. The owner checks that everything is working.
3. The shop finds the instructions for opening.
4. Employees arrive and prepare everything.
5. Services such as billing and security are started.
6. Finally, customers can enter.

A computer does something very similar.

```text
Press Power Button
       ↓
Power Supply
       ↓
BIOS / UEFI
       ↓
POST
       ↓
Find Bootable Device
       ↓
Boot Loader
       ↓
Operating System Kernel
       ↓
System Services
       ↓
Login Screen / Desktop
```

---

# 2. Step 1 — Power Supply Initialization

When we press the **Power Button**, the computer starts receiving power.

The **Power Supply Unit (PSU)** provides electricity to important components such as:

* Motherboard
* CPU
* RAM
* SSD / Hard Disk
* Cooling fans

```text
Power Button
     ↓
    PSU
     ↓
 ┌───────────────┐
 │ Motherboard   │
 │ CPU           │
 │ RAM           │
 │ SSD / HDD     │
 │ Fans          │
 └───────────────┘
```

### Simple Example

Imagine turning on a car.

Before the car can move, the engine and other components need power.

Similarly, the computer's hardware must receive stable power before it can start working.

> **Placement Point:** Power supply is the starting point of the boot process. The OS has not started yet.

---

# 3. Step 2 — BIOS / UEFI Starts

Once the hardware receives power, **BIOS or UEFI firmware** starts running.

### What is BIOS/UEFI?

It is firmware stored on the motherboard that helps initialize the computer and begin the boot process.

```text
Power
  ↓
BIOS / UEFI
  ↓
Check Hardware
  ↓
Initialize Hardware
  ↓
Find Boot Device
```

Modern computers generally use **UEFI**, while older systems commonly used **BIOS**.

---

## POST — Power-On Self-Test

One of the first things BIOS/UEFI performs is **POST**.

**POST = Power-On Self-Test**

It checks whether important hardware is available and working correctly.

For example:

* CPU
* RAM
* Display/video hardware
* Storage devices

If something goes wrong, the system may:

* Display an error message
* Produce beep codes
* Stop the boot process

### Simple Example

Imagine a teacher entering a classroom and checking:

> "Are all students present? Is the projector working? Is everything ready?"

POST performs a similar basic hardware check.

---

# 4. Step 3 — Finding the Boot Device

After POST succeeds, BIOS/UEFI needs to find **where the operating system is stored**.

The computer checks devices according to the configured **boot order**.

For example:

```text
Boot Order:

1. SSD
2. USB
3. DVD
```

If the SSD contains a bootable operating system, the system can continue from there.

---

## MBR vs GPT and UEFI

This is an important placement topic.

### Traditional BIOS System

Traditional BIOS commonly works with **MBR (Master Boot Record)**.

```text
Disk
┌──────────────────────────────┐
│ MBR                          │
│ Boot Code + Partition Info   │
├──────────────────────────────┤
│ Operating System             │
└──────────────────────────────┘
```

### Modern UEFI System

UEFI commonly works with **GPT (GUID Partition Table)** and uses an **EFI System Partition (ESP)** containing boot files.

```text
Disk
┌──────────────────────────────┐
│ GPT                          │
├──────────────────────────────┤
│ EFI System Partition (ESP)   │
│      ↓                       │
│   Boot Files                 │
├──────────────────────────────┤
│ Operating System             │
└──────────────────────────────┘
```

> **Important:** BIOS/MBR and UEFI/GPT are related concepts, but they are not exactly the same thing. Modern UEFI systems commonly use GPT.

---

# 5. Step 4 — Boot Loader Runs

Once the firmware finds a bootable device, it starts the **boot loader**.

### What is a Boot Loader?

A **boot loader** is a small program whose job is to help load the operating system kernel into memory.

Examples:

* **GRUB** — commonly used with Linux
* **Windows Boot Manager** — used by Windows

```text
BIOS / UEFI
     ↓
Boot Device
     ↓
Boot Loader
     ↓
OS Kernel
```

### Simple Example

Imagine you have a book containing thousands of pages.

The boot loader is like the person who:

> "Finds the correct book and opens it to the required starting page."

---

# 6. Step 5 — Kernel is Loaded

Now the **Operating System Kernel** is loaded into RAM.

The kernel is the core part of the operating system.

It manages important resources such as:

* CPU
* Memory
* Hardware devices
* Processes
* Drivers
* System resources

```text
Boot Loader
     ↓
Load Kernel
     ↓
Kernel starts running
     ↓
Initialize OS
```

### Connection with Previous Topic

Remember:

> **Kernel = Core of the Operating System**

The boot loader's important job is to get the kernel loaded so that the operating system can take control.

---

# 7. Step 6 — Init / systemd Starts

After the kernel finishes its initial setup, it starts the first major user-space process.

Traditionally, this was called **init**.

Modern Linux systems commonly use **systemd**.

```text
Kernel
   ↓
init / systemd
   ↓
Start required services
   ↓
Login / User Interface
```

The init system helps start other processes and services required by the operating system.

---

## Runlevels

Traditional Linux systems used **runlevels** to represent different operating modes.

For example:

* **Runlevel 3** → Multi-user mode with networking, generally text-based
* **Runlevel 5** → Multi-user mode with graphical interface

Modern Linux systems using `systemd` generally use **targets** instead of traditional runlevels, although runlevel concepts are still useful for understanding older systems.

---

# 8. Step 7 — System Services and Daemons Start

The init/systemd process starts various background services.

These can include:

* Networking
* Security services
* Printing
* Display services
* Other system services

These background processes are often called **daemons** in Unix/Linux terminology.

```text
systemd / init
      ↓
 ┌────┼──────────┐
 ↓    ↓          ↓
Network Security Display
Service Service  Service
```

### Simple Example

Think about a hotel opening.

Before guests enter:

* Security starts
* Lights are turned on
* Reception becomes active
* Other services are prepared

Similarly, the OS starts required background services before giving full control to the user.

---

# 9. Step 8 — Login Screen Appears

After the required system services start, the computer can present a login interface.

For example:

```text
System Boot
     ↓
Kernel
     ↓
System Services
     ↓
Display / Login Manager
     ↓
Login Screen
```

The user enters their username/password or uses another authentication method.

---

# 10. Step 9 — Desktop Environment Loads

After login, the operating system loads the user's graphical environment.

Examples:

* Windows Desktop
* macOS Finder
* Linux GNOME
* Linux KDE

Now the computer is ready for normal interaction.

```text
Power ON
   ↓
Firmware
   ↓
POST
   ↓
Boot Loader
   ↓
Kernel
   ↓
System Services
   ↓
Login
   ↓
Desktop
   ↓
Ready to Use
```

---

# BIOS/UEFI — Important Functions

BIOS/UEFI performs several important jobs during startup.

| Function                    | Purpose                                                 |
| --------------------------- | ------------------------------------------------------- |
| **POST**                    | Checks important hardware                               |
| **Hardware Initialization** | Prepares hardware for use                               |
| **Boot Selection**          | Finds a bootable device                                 |
| **Boot Configuration**      | Allows boot order and other settings                    |
| **Secure Boot**             | Helps verify trusted boot software on supported systems |
| **Firmware Settings**       | Provides configuration options                          |

Modern UEFI systems can also work with security technologies such as **Secure Boot** and TPM-related platform features.

---

# Complete Boot Process

Here is the entire process in one diagram:

```text
              Press Power Button
                       ↓
              Power Supply (PSU)
                       ↓
                BIOS / UEFI
                       ↓
                     POST
                       ↓
             Hardware Initialization
                       ↓
              Find Bootable Device
                       ↓
             Boot Loader Executes
                       ↓
               Kernel Loaded
                       ↓
              Kernel Initialization
                       ↓
                init / systemd
                       ↓
             Start System Services
                       ↓
                 Login Screen
                       ↓
                  User Login
                       ↓
              Desktop Environment
                       ↓
                 System Ready
```

---

# Technical Explanation

Now let's look at the process from an operating-system perspective.

When the machine is powered on, the processor begins execution from a predefined firmware entry point. Firmware such as **BIOS or UEFI** initializes the platform and performs hardware checks.

After initialization, the firmware identifies a bootable device according to its configuration.

In a traditional BIOS-based system, boot information is associated with the **MBR**. In a modern UEFI system, firmware typically loads a boot application from the **EFI System Partition (ESP)** on a GPT-formatted disk.

The boot loader then loads the operating system kernel into memory and transfers control to it.

The kernel performs its initialization, including setting up memory management, CPU-related structures, device support, and other core operating-system facilities.

After kernel initialization, the system starts the initial user-space process, traditionally `init` and commonly `systemd` on modern Linux systems.

That process starts required services and eventually provides the login environment through which the user can interact with the system.

---

# Boot Process vs Operating System

A common interview confusion is:

> **Does the OS start immediately when we press the power button?**

**No.**

There are several steps before the OS kernel starts.

```text
Power
 ↓
Firmware
 ↓
Hardware Checks
 ↓
Boot Loader
 ↓
Operating System Kernel
 ↓
User-Space Services
 ↓
User
```

So, **BIOS/UEFI is firmware, not the operating system.**

---

# Placement-Focused Questions

### 1. What is booting?

**Answer:**
Booting is the process of starting a computer and loading the operating system into memory so that the system becomes ready for use.

---

### 2. What is POST?

**Answer:**
POST stands for **Power-On Self-Test**. It is performed by firmware during startup to check whether important hardware components are functioning properly.

---

### 3. What is BIOS?

**Answer:**
BIOS stands for **Basic Input/Output System**. It is firmware that initializes hardware and begins the boot process.

---

### 4. What is UEFI?

**Answer:**
UEFI stands for **Unified Extensible Firmware Interface**. It is modern firmware that initializes hardware and loads boot applications, commonly from the EFI System Partition.

---

### 5. What is the role of a boot loader?

**Answer:**
The boot loader loads the operating system kernel into memory and transfers control to it.

---

### 6. What is the difference between BIOS and UEFI?

| BIOS                         | UEFI                      |
| ---------------------------- | ------------------------- |
| Older firmware interface     | Modern firmware interface |
| Commonly associated with MBR | Commonly used with GPT    |
| More limited                 | More feature-rich         |
| Traditional boot process     | Modern boot process       |

---

### 7. What is MBR?

**Answer:**
MBR stands for **Master Boot Record**. In traditional BIOS-based systems, it is located at the beginning of a boot disk and contains boot-related information.

---

### 8. What is GPT?

**Answer:**
GPT stands for **GUID Partition Table**. It is a modern partitioning scheme commonly used with UEFI systems.

---

### 9. What is the difference between a boot loader and a kernel?

**Boot Loader:**

* Starts before the OS kernel
* Loads the kernel
* Transfers control to the kernel

**Kernel:**

* Core of the operating system
* Manages CPU, memory, devices, processes, etc.

```text
Boot Loader
     ↓
Loads
     ↓
Kernel
     ↓
Manages System
```

---

### 10. What happens after the kernel starts?

The kernel performs its initialization and eventually starts the initial user-space process such as **init/systemd**, which starts system services and helps bring the system to a usable state.

---

# Quick Revision

Remember the boot process as:

> **Power → Firmware → POST → Boot Loader → Kernel → init/systemd → Services → Login → Desktop**

| Stage            | Main Job                                     |
| ---------------- | -------------------------------------------- |
| **PSU**          | Provides power                               |
| **BIOS/UEFI**    | Initializes hardware and starts boot process |
| **POST**         | Checks important hardware                    |
| **Boot Device**  | Provides boot files                          |
| **Boot Loader**  | Loads the kernel                             |
| **Kernel**       | Takes control and manages the system         |
| **init/systemd** | Starts user-space services                   |
| **Services**     | Prepare the system                           |
| **Login**        | Authenticates the user                       |
| **Desktop**      | Provides the user interface                  |

---

# One-Minute Interview Answer

> **When we press the power button, the PSU provides power to the computer's components. BIOS or UEFI firmware then starts and performs POST to check important hardware. After that, the firmware finds a bootable device according to the boot configuration. It starts the boot loader, such as GRUB or Windows Boot Manager. The boot loader loads the operating system kernel into RAM and transfers control to it. The kernel initializes the system and starts the initial user-space process such as init or systemd. System services are then started, followed by the login screen and finally the desktop environment. This complete sequence is called the boot process.**

---

## Key Takeaways

* **Booting** = Starting the computer and loading the OS.
* **BIOS/UEFI** = Firmware that begins the boot process.
* **POST** = Checks important hardware.
* **Boot Loader** = Loads the OS kernel.
* **Kernel** = Core of the operating system.
* **init/systemd** = Starts user-space services.
* **MBR** = Traditional boot/partition structure associated with BIOS.
* **GPT** = Modern partitioning scheme commonly used with UEFI.
* **Boot process ends with a usable system**, typically showing a login screen and then the user's desktop.
