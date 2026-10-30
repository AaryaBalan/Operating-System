# Unix File System – Complete Beginner-to-Advanced Guide

**Level:** Beginner → Intermediate → Advanced
**Subject:** Operating Systems
**Focus:** Concepts, internal working, practical commands, examinations, and interviews.

---

## Table of Contents

1. [Introduction to Unix File System](#1-introduction-to-unix-file-system)
2. [What Is Unix?](#2-what-is-unix)
3. [What Is the Unix File System?](#3-what-is-the-unix-file-system)
4. [How Unix Organizes Files](#4-how-unix-organizes-files)
5. [Understanding the Root Directory (`/`)](#5-understanding-the-root-directory-)
6. [Understanding Important Unix Directories](#6-understanding-important-unix-directories)
7. [What Is an Inode?](#7-what-is-an-inode)
8. [What Is a Pathname?](#8-what-is-a-pathname)
9. [Types of Unix Files](#9-types-of-unix-files)
   - [Ordinary Files](#91-ordinary-files)
   - [Directories](#92-directories)
   - [Special Files](#93-special-files)
   - [Pipes](#94-pipes)
   - [Sockets](#95-sockets)
   - [Symbolic Links](#96-symbolic-links)
10. [Character Special Files vs Block Special Files](#10-character-special-files-vs-block-special-files)
11. [Understanding Pipes in Detail](#11-understanding-pipes-in-detail)
12. [Understanding Unix Sockets](#12-understanding-unix-sockets)
13. [Understanding Symbolic Links](#13-understanding-symbolic-links)
14. [File Permissions in Unix](#14-file-permissions-in-unix)
15. [Understanding `ls -l` Output](#15-understanding-ls-l-output)
16. [Important Unix Commands](#16-important-unix-commands)
17. [How Unix Handles a File Request](#17-how-unix-handles-a-file-request)
18. [Advantages of Unix File System](#18-advantages-of-unix-file-system)
19. [Disadvantages of Unix File System](#19-disadvantages-of-unix-file-system)
20. [Unix File System vs Windows File Organization](#20-unix-file-system-vs-windows-file-organization)
21. [Real-World Examples](#21-real-world-examples)
22. [Interview Questions and Answers](#22-interview-questions-and-answers)
23. [Practical Exercises](#23-practical-exercises)
24. [Revision Cheat Sheet](#24-revision-cheat-sheet)
25. [Explain Unix File System in 60 Seconds](#25-explain-unix-file-system-in-60-seconds)
26. [Final Self-Assessment](#26-final-self-assessment)

---

# 1. Introduction to Unix File System

## 1.1 First, imagine a house

Imagine that you have a big house.

Your house contains:

- A bedroom.
- A kitchen.
- A bathroom.
- A living room.
- A storage room.

Inside each room, you keep different things.

For example:

```text
HOUSE
 |
 +-- Bedroom
 |     +-- Books
 |     +-- Clothes
 |
 +-- Kitchen
 |     +-- Plates
 |     +-- Spoons
 |
 +-- Living Room
       +-- Television
       +-- Sofa
```

Why do we organize things like this?

Because finding something becomes easier.

Now imagine a computer with thousands or millions of files.

It needs an organized structure to manage all of them.

**Unix uses a hierarchical file-system structure to organize information.**

## 1.2 What is a Unix File System?

A Unix file system is a logical method of organizing and storing information so that the operating system can manage and access it efficiently.

In Unix:

1. Data is represented through files and other file-system objects.
2. Files are organized using directories.
3. Directories follow a hierarchical tree structure.
4. The root directory (`/`) is the topmost directory.
5. Permissions control access to files and directories.

### Simple definition for exams

A Unix file system is a hierarchical structure used to organize, store, retrieve, and manage files and directories. It uses a root directory, inodes, pathnames, and permissions to manage information.

## 1.3 Why is the Unix file system important?

Imagine you are using a Linux computer.

You have:

- Personal documents.
- Operating-system programs.
- System configuration files.
- Device information.
- Temporary files.
- System logs.

Unix needs a way to organize all these resources.

The file system provides that organization.

**Remember this:**

The Unix file system is not just a place where documents are stored. It is an organized environment through which the operating system accesses many kinds of resources.

---

# 2. What Is Unix?

## 2.1 Definition

Unix is a family of multiuser, multitasking operating systems originally developed at Bell Labs.

Unix introduced important operating-system ideas, including:

- A hierarchical file system.
- A command-line interface.
- Processes and multitasking.
- Permissions and user accounts.
- Pipes for connecting commands.
- A common file-oriented interface for many resources.

The expansion commonly given for UNIX is **UNiplexed Information and Computing System**.

## 2.2 What does multiuser mean?

Multiuser means multiple users can use the same computer system, subject to the operating system's scheduling and access-control rules.

Example:

```text
Computer
  |
  +-- User A
  |     +-- notes.txt
  |
  +-- User B
  |     +-- project.py
  |
  +-- User C
        +-- photo.jpg
```

Each user can have their own files and permissions.

## 2.3 What does multitasking mean?

Multitasking means the operating system can manage multiple running tasks.

For example:

- A browser is running.
- A music player is running.
- A text editor is running.
- A file is downloading.

The operating system manages their execution and resource usage.

## 2.4 Unix and Linux

Are Unix and Linux the same?

No.

| Unix                                              | Linux                                                     |
| ------------------------------------------------- | --------------------------------------------------------- |
| Original Unix systems were developed at Bell Labs | Linux was created by Linus Torvalds                       |
| Has various historical and modern implementations | Is an open-source Unix-like operating-system kernel       |
| Examples include AIX and HP-UX                    | Examples include Ubuntu, Debian, and Fedora distributions |

Linux adopts many Unix concepts, including:

- Hierarchical directories.
- File permissions.
- Inodes.
- Pipes.
- Device files.
- System calls.

This is why studying Unix file-system concepts helps you understand Linux.

---

# 3. What Is the Unix File System?

Let's understand the most important characteristics.

## 3.1 Everything is treated as a file

One famous Unix design principle is:

**Everything is a file.**

What does this mean?

In traditional file-oriented Unix interfaces, many different resources can be accessed using file descriptors and file operations.

For example:

| Resource                     | Representation        |
| ---------------------------- | --------------------- |
| Text document                | Ordinary file         |
| Directory                    | Directory file        |
| Keyboard                     | Character device file |
| Disk device                  | Block device file     |
| Communication channel        | Pipe                  |
| Local communication endpoint | Unix socket           |

This does not mean every resource behaves exactly like an ordinary text document.

It means Unix provides a common file-oriented way to access many resources.

### Real-life example

Imagine a hotel.

You interact with:

- A guest room.
- A storage room.
- An electricity control panel.
- A communication system.

These are different things, but the hotel provides organized ways to access them.

Unix similarly uses file-oriented interfaces for different types of resources.

## 3.2 Files are organized into directories

A directory is similar to a folder.

Example:

```text
College/
    OperatingSystems/
        notes.txt
        deadlock.pdf

    Mathematics/
        algebra.pdf
        calculus.pdf
```

Here:

- `College` is a directory.
- `OperatingSystems` is a subdirectory.
- `notes.txt` is an ordinary file.

## 3.3 Hierarchical tree structure

Unix directories follow a hierarchical structure.

Think about a family tree:

```text
Grandfather
    |
    +-- Father
    |     |
    |     +-- Child A
    |     +-- Child B
    |
    +-- Uncle
          |
          +-- Child C
```

Unix uses a similar parent-child organization.

```text
/
|
+-- home
|    |
|    +-- student
|         |
|         +-- notes.txt
|
+-- etc
|
+-- usr
|
+-- var
```

Everything starts from the root directory.

---

# 4. How Unix Organizes Files

The Unix file system can be understood through three important concepts:

1. Directories.
2. Inodes.
3. Pathnames.

Let's understand each one.

### Example

Suppose you have:

```text
/home/student/college/OS.txt
```

This path identifies a file.

The directory hierarchy helps find the file.

The inode contains important information about the file.

The pathname tells the operating system how to reach it through the directory hierarchy.

Conceptually:

```text
Pathname
   |
   v
Directory lookup
   |
   v
Inode
   |
   v
File metadata and data locations
   |
   v
File contents
```

We will understand inodes and pathnames in detail shortly.

---

# 5. Understanding the Root Directory (`/`)

## 5.1 What is the root directory?

The root directory is the topmost directory in a Unix file-system hierarchy.

Its symbol is:

```text
/
```

Think of the root directory as the main trunk of a tree.

All directories in the normal Unix directory hierarchy are ultimately located beneath this root.

Example:

```text
                 /
                 |
       +---------+---------+
       |         |         |
      home      etc       usr
       |         |         |
    student   config    programs
       |
     notes.txt
```

## 5.2 Why is the root directory important?

Because it provides the starting point for absolute paths.

For example:

```text
/home/student/notes.txt
```

The path begins at `/`.

## 5.3 Root directory versus root user

Do not confuse these two terms.

| Root directory                      | Root user                              |
| ----------------------------------- | -------------------------------------- |
| The top-level directory             | The administrator account              |
| Represented by `/`                  | Commonly has username `root`           |
| Organizes the file-system hierarchy | Has powerful administrative privileges |

The root user's home directory is normally:

```text
/root
```

The root directory is:

```text
/
```

They are completely different concepts.

---

# 6. Understanding Important Unix Directories

This is a very important topic for operating-system exams and Linux interviews.

You should understand what each directory stores and why it exists.

Imagine the Unix operating system as a large organization.

Different departments have different responsibilities.

The Unix directory structure works similarly.

## 6.1 Important directory structure

```text
/
|
+-- bin
+-- boot
+-- dev
+-- etc
+-- home
+-- lib
+-- media
+-- mnt
+-- proc
+-- root
+-- tmp
+-- usr
|    |
|    +-- bin
|    +-- include
|    +-- lib
|
+-- var
     |
     +-- log
     +-- mail
     +-- spool
     +-- tmp
```

## 6.2 Explanation of important directories

### 1. `/` — Root directory

\<badge color="success">Very important\</badge>

The root directory is the starting point of the entire directory hierarchy.

Example:

```text
/
├── home
├── etc
├── usr
└── var
```

**Remember:** Everything in the normal directory hierarchy starts from `/`.

### 2. `/bin` — Essential binary programs

`bin` stands for binaries.

It traditionally contains fundamental commands needed for essential system operation and user activities.

Examples include:

```text
ls
cp
```

What do they do?

- `ls` lists files and directories.
- `cp` copies files.

**Analogy:** A toolbox containing basic tools required for everyday work.

Modern Linux systems may make `/bin` a symbolic link to `/usr/bin`.

### 3. `/boot` — Boot files

This directory contains files needed for the system boot process.

Examples include:

- Kernel images.
- Bootloader-related files.
- Initial RAM filesystem images.

**Analogy:** The starting instructions needed to wake up the computer.

### 4. `/dev` — Device files

`dev` stands for devices.

It contains representations of devices and pseudo-devices.

Examples on Linux include:

```text
/dev/null
/dev/tty
/dev/sda
```

- `/dev/null` is a special device that discards written data.
- `/dev/tty` represents a controlling terminal in relevant contexts.
- `/dev/sda` may represent a storage device.

Not every computer has exactly these device names.

**Analogy:** A control center through which the operating system interacts with devices.

### 5. `/etc` — Configuration files

This directory stores system-wide configuration files.

Examples:

```text
/etc/hosts
/etc/passwd
/etc/fstab
```

These files contain configuration information used by the operating system and applications.

**Analogy:** The settings room of a computer.

### 6. `/home` — User home directories

This directory normally contains users' personal home directories.

Example:

```text
/home
  |
  +-- student
  |     +-- notes.txt
  |     +-- photo.jpg
  |
  +-- developer
        +-- project.py
```

Each user can maintain personal files and directories.

**Analogy:** Separate bedrooms for different users.

### 7. `/lib` — Libraries and essential support files

This directory traditionally contains essential shared libraries and other important files, such as kernel modules on some systems.

Libraries provide reusable functions used by programs.

Example:

A program may depend on a shared library to perform certain operations.

**Analogy:** A collection of reusable tools that other programs need.

Modern systems may use `/lib` as a symbolic link to a directory under `/usr`.

### 8. `/media` — Removable media

This directory is commonly used as a mount point for removable storage devices.

Examples:

- USB drives.
- Memory cards.
- External media.

Example:

```text
/media/student/USB
```

A USB drive may become accessible through this directory after mounting.

**Analogy:** A temporary parking area for external storage devices.

### 9. `/mnt` — Mount directory

`mnt` stands for mount.

It provides a conventional location for temporarily mounting file systems.

Example:

```text
/mnt/external_drive
```

An administrator might mount another storage volume there.

**Difference between `/media` and `/mnt`:**

| `/media`                           | `/mnt`                                          |
| ---------------------------------- | ----------------------------------------------- |
| Commonly used for removable media  | Commonly used for manually mounted file systems |
| Often used by desktop environments | Often used by administrators                    |

These are conventions, not strict rules.

### 10. `/proc` — Process information

`/proc` is a virtual file system on Linux that exposes information about processes and the running kernel.

Example:

```text
/proc/cpuinfo
/proc/meminfo
/proc/1
```

- `/proc/cpuinfo` provides processor information.
- `/proc/meminfo` provides memory information.
- `/proc/1` provides information associated with process ID 1.

**Important:** These are not ordinary files stored permanently on a disk. Much of the information is generated dynamically by the kernel.

**Analogy:** A live information dashboard showing what is happening inside the computer.

### 11. `/root` — Administrator's home directory

This is the home directory of the root user.

Example:

```text
/root
```

It is separate from `/home`.

**Remember:**

- `/` = Root directory.
- `/root` = Root user's home directory.

### 12. `/tmp` — Temporary files

This directory stores temporary files.

Examples:

- Temporary application data.
- Intermediate files.
- Short-lived working data.

Some systems clear `/tmp` during startup or use temporary memory-backed storage.

Do not assume every system deletes everything in `/tmp` after every reboot.

**Analogy:** A rough-work notebook that may be cleaned periodically.

### 13. `/usr` — User-space programs and resources

Historically, its name is associated with user-related directories, but its modern purpose is different.

It commonly contains:

- Executable programs.
- Libraries.
- Shared resources.
- Documentation.
- Development files.

Example:

```text
/usr
  |
  +-- bin
  +-- include
  +-- lib
  +-- share
```

**Analogy:** A large library of installed software and resources.

### 14. `/usr/bin` — User programs

Contains many executable programs supplied by the operating system or installed software.

Examples include commands and applications.

### 15. `/usr/include` — Header files

This directory contains development header files used by programmers.

For example, C and C++ programs can include header files using:

```c
#include <stdio.h>
```

Header files provide declarations and other information needed during compilation.

**Analogy:** Instruction manuals that help programs understand available functions.

### 16. `/usr/lib` — Libraries and program data

Contains libraries and supporting files used by programs.

It helps applications access reusable functionality.

### 17. `/var` — Variable data

`var` stands for variable.

This directory stores data that changes frequently or grows over time.

Examples:

- Logs.
- Mail-related data.
- Spool files.
- Application state.

**Analogy:** A notice board where new information is continuously added or updated.

### 18. `/var/log` — Log files

Contains system and application logs.

Logs help administrators understand what happened inside a system.

Example:

```text
/var/log/syslog
/var/log/auth.log
```

The exact files depend on the Linux distribution and logging configuration.

### 19. `/var/mail` — Mail storage

Traditionally used to store incoming mail for users.

Its exact implementation varies. On some systems it may be linked to another mail-storage directory.

### 20. `/var/spool` — Queued tasks

Stores data waiting to be processed.

Examples:

- Print jobs.
- Mail queues.
- Other queued operations.

**Analogy:** A waiting line where tasks stay until the system processes them.

### 21. `/var/tmp` — Longer-lived temporary files

Stores temporary files that are generally expected to survive system reboots longer than files in `/tmp`.

The exact cleanup policy depends on the system.

## 6.3 Complete directory comparison table

| Directory      | Main purpose                           | Easy memory trick       |
| -------------- | -------------------------------------- | ----------------------- |
| `/`            | Root of the hierarchy                  | Main tree trunk         |
| `/bin`         | Fundamental programs                   | Basic toolbox           |
| `/boot`        | Boot-related files                     | Startup instructions    |
| `/dev`         | Device representations                 | Device center           |
| `/etc`         | Configuration files                    | Settings room           |
| `/home`        | User home directories                  | Personal rooms          |
| `/lib`         | Essential libraries and support        | Reusable tools          |
| `/media`       | Removable media mount points           | External-device parking |
| `/mnt`         | Mount points                           | Manual mounting area    |
| `/proc`        | Virtual process and kernel information | Live dashboard          |
| `/root`        | Root user's home                       | Administrator's room    |
| `/tmp`         | Temporary files                        | Rough-work area         |
| `/usr`         | Programs and shared resources          | Software library        |
| `/usr/bin`     | User-space executables                 | Program collection      |
| `/usr/include` | Development headers                    | Programming manuals     |
| `/usr/lib`     | Libraries and supporting files         | Program support         |
| `/var`         | Frequently changing data               | Changing information    |
| `/var/log`     | Log files                              | System diary            |
| `/var/mail`    | Mail storage                           | Mailbox                 |
| `/var/spool`   | Queued work                            | Waiting line            |
| `/var/tmp`     | Longer-lived temporary files           | Longer-term rough work  |

### Important exam question

**What is the difference between `/tmp` and `/var/tmp`?**

`/tmp` is commonly used for temporary files that may be cleared relatively frequently. `/var/tmp` is intended for temporary files that should generally survive reboots.

---

# 7. What Is an Inode?

\<badge color="success">Extremely important\</badge>

This is one of the most important concepts in Unix file systems.

Let's understand it slowly.

## 7.1 First, imagine a library

Imagine that a library has 10,000 books.

Every book has:

- A title.
- An author.
- A size.
- A location.
- Other information.

The library maintains a record describing each book.

Now imagine that a book has an identification number:

```text
Book ID: 105
Title: Operating Systems
Author: XYZ
Location: Shelf 5
```

Even if the book's display name changes, its identification record can still identify the same book.

Unix uses a similar idea through an inode.

## 7.2 Definition of inode

An **inode (index node)** is a data structure used by Unix-like file systems to store metadata about a file-system object and information needed to locate its data.

An inode can store information such as:

- File type.
- Permissions.
- Owner ID.
- Group ID.
- File size.
- Timestamps.
- Link count.
- Pointers or other information identifying where file data is stored.

The exact inode structure depends on the file system.

## 7.3 What does an inode NOT normally store?

An inode does not normally store the file's ordinary filename.

The filename is maintained by directory entries, which associate a name with an inode number.

This is a very important distinction.

| Directory entry                            | Inode                                                                        |
| ------------------------------------------ | ---------------------------------------------------------------------------- |
| Associates a filename with an inode number | Stores metadata about the file-system object                                 |
| Helps locate an object by name             | Helps identify and manage the object                                         |
| Example: `notes.txt → inode 105`           | Example: inode 105 contains permissions, size, and data-location information |

## 7.4 Inode example

Suppose you create:

```text
notes.txt
```

The file system might create a directory entry:

```text
notes.txt  --->  Inode 105
```

The inode might conceptually contain:

```text
Inode 105
----------------------
File type: Regular file
Owner: 1000
Permissions: 644
Size: 2048 bytes
Data locations: ...
Link count: 1
----------------------
```

The actual contents are stored in data blocks, and the inode contains the information needed to locate them.

## 7.5 How does Unix access a file?

Suppose you request:

```text
/home/student/notes.txt
```

A simplified process is:

1. Begin at the root directory.
2. Find the `home` entry.
3. Find the `student` entry.
4. Find the `notes.txt` entry.
5. Resolve its inode number.
6. Read the inode information.
7. Locate the file data.
8. Return the requested content.

Conceptual diagram:

```text
        Pathname
            |
            v
       Directory
            |
            v
       File name
            |
            v
       Inode number
            |
            v
          Inode
            |
            v
      Data locations
            |
            v
       File content
```

## 7.6 Can two filenames point to the same inode?

Yes!

This is called a **hard link**.

Example:

```text
notes.txt  ------+
                 |
                 v
              Inode 105
                 ^
                 |
backup.txt ------+
```

Both directory entries refer to the same inode.

This means they are two names for the same underlying file object.

If you change the file contents through one name, the change is visible through the other name.

Removing one hard link does not necessarily remove the file's data. The data becomes eligible for reclamation when its link count reaches zero and no relevant open references remain.

## 7.7 What is an inode number?

An inode number identifies an inode within its file system.

You can display inode numbers using:

```bash
ls -i
```

Example:

```text
105 notes.txt
106 photo.jpg
107 assignment.pdf
```

You can also use:

```bash
stat notes.txt
```

This displays detailed file metadata, including its inode number on supported file systems.

### Important points about inodes

1. Inodes store metadata.
2. Inodes normally do not store ordinary filenames.
3. Directory entries associate names with inode numbers.
4. A file can have multiple hard links.
5. An inode number is meaningful within its file system, not a universal global identifier.

### One-line interview answer

An inode is a Unix file-system data structure that stores metadata and information about the location of a file's data, while directory entries associate filenames with inode numbers.

---

# 8. What Is a Pathname?

## 8.1 Definition

A pathname is a sequence of directory names that identifies the location of a file or directory in a file-system hierarchy.

Example:

```text
/home/student/college/notes.txt
```

Let's break it down:

| Component   | Meaning         |
| ----------- | --------------- |
| `/`         | Root            |
| `home`      | First directory |
| `student`   | User directory  |
| `college`   | Subdirectory    |
| `notes.txt` | File            |

## 8.2 Absolute pathname

An absolute pathname starts from the root directory.

Example:

```text
/home/student/notes.txt
```

It identifies the location independently of your current working directory.

## 8.3 Relative pathname

A relative pathname is interpreted from the current working directory.

Suppose your current location is:

```text
/home/student
```

You can access the file using:

```text
notes.txt
```

Or:

```text
college/notes.txt
```

## 8.4 Important path symbols

| Symbol | Meaning                                        |
| ------ | ---------------------------------------------- |
| `/`    | Root directory or path separator               |
| `.`    | Current directory                              |
| `..`   | Parent directory                               |
| `~`    | Current user's home directory in common shells |

Example:

```bash
cd ..
```

Moves to the parent directory.

```bash
cd ~
```

Moves to your home directory in a typical shell.

## 8.5 Pathname and inode relationship

The source notes describe a pathname being resolved to an inode.

The more precise explanation is that the operating system resolves each component through directory entries until it reaches the intended file-system object.

Also, pathnames are not necessarily unique: hard links and symbolic links can provide multiple names or paths to an object.

---

# 9. Types of Unix Files

\<badge color="success">Very important for exams\</badge>

Unix traditionally identifies six major file types:

1. Ordinary files.
2. Directories.
3. Special files.
4. Pipes.
5. Sockets.
6. Symbolic links.

Let's learn them one by one.

## 9.1 Ordinary Files

### Definition

An ordinary file is a file that stores data, text, or program instructions.

Examples:

```text
notes.txt
photo.jpg
program.c
assignment.pdf
```

These are the files you normally work with.

### Characteristics

- Store ordinary content.
- Can contain text, images, programs, and other data.
- Are organized inside directories.
- Do not themselves contain other files as directories do.

### Example

Suppose you create:

```text
OS_notes.txt
```

It contains:

```text
Unix is an operating system family.
It uses a hierarchical file system.
```

This is an ordinary file.

### How is it represented?

In the long-format output of:

```bash
ls -l
```

An ordinary file is represented by:

```text
-
```

Example:

```text
-rw-r--r-- 1 student student 200 Oct 30 notes.txt
```

The first character is `-`.

**Memory trick:** Ordinary file = actual user content.

---

## 9.2 Directories

### Definition

A directory is a special file-system object that organizes filenames and references to other objects.

It is similar to a folder.

Example:

```text
College/
    |
    +-- notes.txt
    +-- photo.jpg
    |
    +-- OperatingSystems/
          +-- deadlock.pdf
```

### Characteristics

1. Organize files.
2. Can contain entries for other directories.
3. Maintain names and associated file references.
4. Support the hierarchical tree structure.
5. Help locate files.

A traditional Unix directory entry associates a filename with an inode number.

### How is it represented?

In:

```bash
ls -l
```

A directory is represented by:

```text
d
```

Example:

```text
drwxr-xr-x 2 student student 4096 Oct 30 College
```

The first character is `d`.

**Memory trick:** Directory = organizer of files.

---

## 9.3 Special Files

Special files represent devices or device-like interfaces.

Examples:

- A keyboard.
- A terminal.
- A disk device.
- A printer interface.

They are commonly found under:

```text
/dev
```

There are two classic types:

1. Character special files.
2. Block special files.

Let's understand both in detail in Section 10.

### Representation

In `ls -l`:

- `c` = Character special file.
- `b` = Block special file.

---

## 9.4 Pipes

A pipe provides a communication channel through which the output of one command can become the input of another.

Imagine two people:

```text
Person A ---> Person B
```

Person A sends information, and Person B receives it.

Unix pipes work similarly.

Example:

```bash
who | wc -l
```

Here:

- `who` displays logged-in users.
- `wc -l` counts the lines.
- `|` connects the two commands.

The output of the first command becomes the input of the second.

### Types of pipes

**Anonymous pipe:** Usually created by a process through a system call and used to communicate between related or otherwise connected processes.

**Named pipe (FIFO):** Has a name in the file-system namespace and can be used by processes that open that name.

A named pipe is represented by:

```text
p
```

in `ls -l`.

We will explore pipes more deeply in Section 11.

---

## 9.5 Sockets

A Unix domain socket is a communication endpoint that allows processes to exchange information.

Imagine a restaurant:

```text
Customer
   |
   v
Restaurant counter
   |
   v
Kitchen
```

The customer and kitchen communicate through a common interface.

Similarly, processes can communicate using sockets.

Example:

- A local application communicates with a background service.
- A database client communicates with a database server.
- Two local processes exchange information.

A Unix domain socket commonly uses a pathname such as:

```text
/run/example.sock
```

A socket file is represented by:

```text
s
```

in `ls -l`.

**Important:** Not every socket is a file-system pathname socket. Unix sockets can also be unnamed, and network sockets are a broader category.

---

## 9.6 Symbolic Links

A symbolic link is also called a soft link.

It is a special file-system object that points to another file or directory using a pathname.

Imagine you have a book stored in a cupboard.

Instead of moving the book, you place a note elsewhere saying:

"The book is located in the cupboard."

That note is like a symbolic link.

### Example

Original file:

```text
/home/student/notes.txt
```

Create a symbolic link:

```bash
ln -s /home/student/notes.txt shortcut.txt
```

Now:

```text
notes.txt
shortcut.txt
```

The second name points to the first file through its stored pathname.

### Characteristics

1. A symbolic link has its own name and inode.
2. It stores a pathname pointing to another object.
3. Accessing the link usually follows the target.
4. Deleting the link does not normally delete the target.
5. If the target is moved or deleted, the link can become broken.

### Example

```text
notes.txt <------ shortcut.txt
```

If you delete `shortcut.txt`, `notes.txt` remains.

If you delete `notes.txt`, `shortcut.txt` becomes a dangling symbolic link.

### Representation

In `ls -l`, symbolic links are represented by:

```text
l
```

Example:

```text
lrwxrwxrwx 1 student student 9 Oct 30 shortcut.txt -> notes.txt
```

**Memory trick:** A symbolic link is like a shortcut pointing to another location.

---

# 10. Character Special Files vs Block Special Files

\<badge color="success">Frequently asked comparison\</badge>

This is an important comparison for exams and interviews.

First, understand that a device is a hardware or virtual resource that can be accessed through an operating-system interface.

Unix represents many device interfaces using special files.

Two classic types are character and block devices.

## 10.1 Character special files

A character special file represents a device interface that supports character-oriented or stream-oriented I/O.

Examples:

- Terminal devices.
- Serial devices.
- Keyboard-related device interfaces.

Data is handled as a stream of bytes or characters, according to the device interface.

Example:

```text
/dev/tty
```

### Analogy

Imagine water flowing continuously through a pipe.

You can process the stream as it arrives.

That is similar to character-oriented I/O.

## 10.2 Block special files

A block special file represents a device interface that supports block-oriented I/O.

Examples:

```text
/dev/sda
/dev/nvme0n1
```

These are examples of possible Linux device names.

Data is handled in blocks or sectors according to the device interface.

### Analogy

Imagine a library distributing books in boxes.

Instead of processing one letter at a time, you receive a whole box of information.

That is similar to block-oriented I/O.

## 10.3 Comparison table

| Character special file                            | Block special file             |
| ------------------------------------------------- | ------------------------------ |
| Character- or stream-oriented I/O                 | Block-oriented I/O             |
| Commonly used for terminals and serial interfaces | Commonly used for disks        |
| Often provides sequential stream access           | Supports block-oriented access |
| Represented by `c`                                | Represented by `b`             |

### Important clarification

Do not interpret the difference as character devices always transferring exactly one character per hardware operation. The important distinction is the I/O model exposed by the device interface.

### Exam memory trick

**Character = stream of bytes.**

**Block = blocks of data.**

---

# 11. Understanding Pipes in Detail

## 11.1 What is a pipe?

A pipe is an inter-process communication mechanism that allows data to flow from one process to another.

IPC stands for Inter-Process Communication.

It means communication between two or more processes.

### Simple example

Suppose you have:

```bash
who
```

This displays currently logged-in users.

Now suppose you want to count them.

You could manually read the output.

But Unix provides a simpler way:

```bash
who | wc -l
```

Let's understand it.

| Command | Work                     |                          |
| ------- | ------------------------ | ------------------------ |
| `who`   | Displays logged-in users |                          |
| \`      | \`                       | Connects output to input |
| `wc -l` | Counts lines             |                          |

The pipe sends the output of `who` to `wc -l`.

### Flow diagram

```text
Process 1: who
       |
       | Output
       v
     PIPE
       |
       | Input
       v
Process 2: wc -l
       |
       v
   Line count
```

## 11.2 Why do we need pipes?

Without pipes, you might need to:

1. Run the first command.
2. Save its output into a file.
3. Open that file.
4. Give the file contents to another command.

A pipe allows commands to communicate directly without requiring a separate temporary file containing the intermediate output.

## 11.3 Anonymous pipe

An anonymous pipe is generally created by a process using a system call.

In a typical Unix example:

```c
pipe(fd);
```

The call creates a pair of file descriptors:

- One for reading.
- One for writing.

A process can use them to exchange data.

The pipe is usually used between related processes, such as a parent and child.

## 11.4 Named pipe

A named pipe is also called a FIFO.

FIFO means First In, First Out.

It provides a name in the file-system namespace so that processes can open it and communicate.

Create a named pipe:

```bash
mkfifo mypipe
```

Check its type:

```bash
ls -l mypipe
```

It should begin with:

```text
p
```

### Anonymous pipe vs named pipe

| Anonymous pipe                              | Named pipe                                    |
| ------------------------------------------- | --------------------------------------------- |
| Usually created through a system call       | Created with a name                           |
| No ordinary pathname is required            | Has a pathname                                |
| Commonly used between related processes     | Can connect processes that open the same FIFO |
| Usually exists while references remain open | Has a directory entry until removed           |

### Important properties

- A traditional pipe is unidirectional.
- Data is read in the order it is written.
- Pipes are commonly used for communication between processes.
- A pipe is not a permanent file for storing ordinary documents.

---

# 12. Understanding Unix Sockets

## 12.1 What is a socket?

A socket is an endpoint used for communication between processes.

A Unix domain socket allows processes on the same machine to communicate.

### Example

Imagine two applications:

```text
Application A
     |
     v
Unix socket
     |
     v
Application B
```

Application A sends information through the socket, and Application B receives it.

## 12.2 Real-world example

Suppose a local application needs information from a database service running on the same computer.

Instead of writing data into a normal document and repeatedly reading it, the two applications can communicate through a Unix domain socket.

## 12.3 Unix socket example

A socket may appear as:

```text
/run/example.sock
```

Check a socket using:

```bash
ls -l /run/example.sock
```

A pathname-based Unix socket commonly appears with:

```text
s
```

as its file-type character.

## 12.4 Socket vs pipe

| Pipe                                  | Socket                                         |
| ------------------------------------- | ---------------------------------------------- |
| Provides a data communication channel | Provides a communication endpoint              |
| Traditional pipe is unidirectional    | Can support bidirectional communication        |
| Commonly used in command pipelines    | Commonly used in client-server communication   |
| Named pipes have file-system names    | Unix domain sockets can have file-system names |

### Memory trick

Pipe = a simple communication channel.

Socket = a communication endpoint with richer communication capabilities.

---

# 13. Understanding Symbolic Links

## 13.1 What is a symbolic link?

A symbolic link is a special file that stores the pathname of another file or directory.

It is also called a soft link.

### Example

Suppose you have:

```text
/home/student/project/report.txt
```

You want to access it from another location.

Instead of copying the file, create a symbolic link:

```bash
ln -s /home/student/project/report.txt myreport.txt
```

Now:

```text
myreport.txt
```

points to the original file.

## 13.2 Why use symbolic links?

They help:

- Avoid unnecessary copies.
- Provide convenient alternative paths.
- Share access to a file from different directory locations.
- Maintain compatibility with expected file locations.

## 13.3 How to create a symbolic link

Command:

```bash
ln -s source destination
```

Example:

```bash
ln -s notes.txt shortcut.txt
```

This creates a link named `shortcut.txt` pointing to `notes.txt`.

## 13.4 How to identify a symbolic link

Command:

```bash
ls -l
```

Example:

```text
lrwxrwxrwx 1 student student 9 Oct 30 shortcut.txt -> notes.txt
```

The first character is `l`.

The arrow shows the target path.

## 13.5 What happens if the original file is deleted?

Suppose:

```text
notes.txt <------ shortcut.txt
```

You delete the original:

```bash
rm notes.txt
```

Now the symbolic link still exists, but its target cannot be found.

It is called a dangling or broken symbolic link.

## 13.6 What happens if the link is deleted?

Suppose you run:

```bash
rm shortcut.txt
```

Only the link is removed. The original file remains.

## 13.7 Hard link vs symbolic link

This is an important additional concept for mastering inodes.

| Hard link                                                    | Symbolic link                           |
| ------------------------------------------------------------ | --------------------------------------- |
| Another directory entry for the same inode                   | A separate file-system object           |
| Points to the same underlying file object                    | Stores a pathname to the target         |
| Normally cannot cross file-system boundaries                 | Can point across file-system boundaries |
| Normally cannot be created for directories by ordinary users | Can point to directories                |
| Target data remains accessible through other hard links      | Can become broken if target disappears  |

### Visual comparison

Hard link:

```text
fileA.txt ----+
              |
              v
           Inode 105
              ^
              |
fileB.txt ----+
```

Symbolic link:

```text
fileA.txt ---> Inode 105

shortcut.txt ---> stores path to fileA.txt
```

**Most important difference:**

A hard link refers to the same inode. A symbolic link stores a pathname to another object.

---

# 14. File Permissions in Unix

Permissions control what users can do with files and directories.

Unix commonly uses three basic permission types:

- Read (`r`).
- Write (`w`).
- Execute (`x`).

## 14.1 What is read permission?

Read permission allows a user to read file contents.

Example:

```text
notes.txt
```

If you have read permission, you can view its contents, subject to other access controls.

## 14.2 What is write permission?

Write permission allows modification of file contents.

For directories, write permission generally allows directory-entry changes when the other required conditions are satisfied.

## 14.3 What is execute permission?

For ordinary files, execute permission allows the file to be executed when it is a suitable executable program or script.

For directories, execute permission allows traversal or searching of the directory, subject to other permissions.

## 14.4 Three permission categories

Unix traditionally represents permissions for:

1. Owner.
2. Group.
3. Others.

Example:

```text
-rwxr-xr--
```

Break it down:

```text
-   rwx   r-x   r--
|    |     |     |
|    |     |     +-- Others
|    |     +-------- Group
|    +-------------- Owner
+------------------- File type
```

Here:

- Owner can read, write, and execute.
- Group can read and execute.
- Others can read.

## 14.5 Numeric permissions

Unix permissions can also be represented using numbers.

| Permission | Number |
| ---------- | -----: |
| Read       |      4 |
| Write      |      2 |
| Execute    |      1 |

Add the values to get the permission number.

Example:

```text
rwx = 4 + 2 + 1 = 7
rw- = 4 + 2 = 6
r-x = 4 + 1 = 5
r-- = 4
```

Therefore:

```bash
chmod 755 script.sh
```

Means:

- Owner: 7 = `rwx`.
- Group: 5 = `r-x`.
- Others: 5 = `r-x`.

Another example:

```bash
chmod 644 notes.txt
```

Means:

- Owner: `rw-`.
- Group: `r--`.
- Others: `r--`.

### Important commands

```bash
ls -l
```

Displays permissions.

```bash
chmod 755 script.sh
```

Changes permissions.

```bash
chown student notes.txt
```

Changes the owner, subject to privileges and system rules.

**Remember:** Permissions help protect information in multiuser systems.

---

# 15. Understanding `ls -l` Output

This command is extremely useful:

```bash
ls -l
```

It displays detailed information about files and directories.

Example:

```text
-rw-r--r-- 1 student student 2048 Oct 30 10:00 notes.txt
drwxr-xr-x 2 student student 4096 Oct 30 10:05 College
lrwxrwxrwx 1 student student    9 Oct 30 10:10 shortcut -> notes.txt
```

Let's understand the first line:

```text
-rw-r--r-- 1 student student 2048 Oct 30 10:00 notes.txt
```

| Part           | Meaning                     |
| -------------- | --------------------------- |
| `-`            | Ordinary file               |
| `rw-r--r--`    | Permissions                 |
| `1`            | Hard-link count             |
| `student`      | Owner                       |
| `student`      | Group                       |
| `2048`         | File size in bytes          |
| `Oct 30 10:00` | Displayed modification time |
| `notes.txt`    | Filename                    |

### First character: File type

| Symbol | File type              |
| ------ | ---------------------- |
| `-`    | Ordinary file          |
| `d`    | Directory              |
| `c`    | Character special file |
| `b`    | Block special file     |
| `p`    | Named pipe             |
| `s`    | Socket                 |
| `l`    | Symbolic link          |

### Practical commands

```bash
ls -l
```

Detailed listing.

```bash
ls -la
```

Detailed listing including hidden entries.

```bash
ls -i
```

Displays inode numbers.

```bash
stat notes.txt
```

Displays detailed metadata.

---

# 16. Important Unix Commands

Let's practice the concepts using commands.

| Command  | Purpose                                   | Example                |
| -------- | ----------------------------------------- | ---------------------- |
| `pwd`    | Display current directory                 | `pwd`                  |
| `ls`     | List files                                | `ls`                   |
| `ls -l`  | Detailed listing                          | `ls -l`                |
| `cd`     | Change directory                          | `cd /home`             |
| `mkdir`  | Create directory                          | `mkdir College`        |
| `touch`  | Create an empty file or update timestamps | `touch notes.txt`      |
| `cat`    | Display file contents                     | `cat notes.txt`        |
| `cp`     | Copy files                                | `cp a.txt b.txt`       |
| `mv`     | Move or rename files                      | `mv a.txt b.txt`       |
| `rm`     | Remove files                              | `rm notes.txt`         |
| `rmdir`  | Remove an empty directory                 | `rmdir College`        |
| `ln -s`  | Create symbolic link                      | `ln -s a.txt b.txt`    |
| `ln`     | Create hard link                          | `ln a.txt b.txt`       |
| `chmod`  | Change permissions                        | `chmod 644 a.txt`      |
| `chown`  | Change ownership                          | `chown user a.txt`     |
| `ls -i`  | Display inode numbers                     | `ls -i`                |
| `stat`   | Display file metadata                     | `stat a.txt`           |
| `mkfifo` | Create a named pipe                       | `mkfifo mypipe`        |
| `find`   | Search for files                          | `find . -name "*.txt"` |

## 16.1 Practice session

Try these commands in a Linux terminal or a safe Linux virtual machine.

Create a directory:

```bash
mkdir UnixPractice
```

Enter it:

```bash
cd UnixPractice
```

Create a file:

```bash
touch notes.txt
```

Write some text:

```bash
echo "Learning Unix File System" > notes.txt
```

Display the content:

```bash
cat notes.txt
```

Check the directory:

```bash
ls -l
```

Check inode information:

```bash
ls -i
```

Create a symbolic link:

```bash
ln -s notes.txt shortcut.txt
```

Check the link:

```bash
ls -l
```

Check file metadata:

```bash
stat notes.txt
```

Change permissions:

```bash
chmod 644 notes.txt
```

Now you have practically explored:

- Directories.
- Ordinary files.
- Symbolic links.
- Inodes.
- Metadata.
- Permissions.

---

# 17. How Unix Handles a File Request

Now let's connect all the concepts.

Suppose you execute:

```bash
cat /home/student/notes.txt
```

What happens internally?

### Step 1: Command execution

The shell interprets your command and starts the `cat` program.

### Step 2: System call

The `cat` program uses operating-system system calls to open the requested file.

### Step 3: Pathname resolution

The operating system resolves:

```text
/home/student/notes.txt
```

It traverses the directory hierarchy.

### Step 4: Directory lookup

It looks up the required directory entries to find the file.

### Step 5: Inode lookup

The directory entry identifies the inode associated with the file.

### Step 6: Permission checking

The operating system checks whether the process is allowed to access the file.

### Step 7: Data access

The file system uses its metadata and storage mechanisms to locate and read the required file data.

### Step 8: Output

The `cat` program writes the contents to standard output, usually the terminal.

### Complete flow

```text
User enters command
        |
        v
       Shell
        |
        v
     cat program
        |
        v
    System calls
        |
        v
  Pathname resolution
        |
        v
  Directory lookup
        |
        v
    Inode lookup
        |
        v
  Permission checking
        |
        v
   Locate file data
        |
        v
     Read contents
        |
        v
  Display on terminal
```

### Why is this important?

It connects several topics:

- Shell.
- Processes.
- System calls.
- Directories.
- Pathnames.
- Inodes.
- Permissions.
- File operations.

This is how operating-system concepts work together.

---

# 18. Advantages of Unix File System

## 18.1 Hierarchical organization

Files are arranged in a tree structure.

This makes it easier to organize and navigate large numbers of files.

## 18.2 Robustness

Unix-like systems have mature file-system designs and recovery mechanisms that can provide reliable storage management.

Actual reliability depends on the implementation and hardware.

## 18.3 Security

Unix permissions allow administrators to control access to files and directories.

This is particularly useful in multiuser environments.

## 18.4 Compatibility

Unix-like systems commonly share many file-system concepts, conventions, and tools.

This makes working across such environments easier, although compatibility is not guaranteed for every file-system format.

## 18.5 Flexibility

Unix supports different types of file-system objects, including:

- Ordinary files.
- Directories.
- Device files.
- Pipes.
- Sockets.
- Symbolic links.

## 18.6 Powerful command-line tools

Users can combine commands to perform complex operations.

Example:

```bash
find . -name "*.txt" | wc -l
```

This finds matching text files and counts the output lines.

---

# 19. Disadvantages of Unix File System

## 19.1 Complexity

Understanding inodes, permissions, mount points, and device files can be difficult for beginners.

## 19.2 Steep learning curve

Users who are unfamiliar with command-line tools may initially find Unix difficult.

## 19.3 Command-line knowledge

Many administration and troubleshooting tasks require knowledge of commands and system configuration.

Graphical interfaces are available on many Unix-like systems, so Unix file management is not limited to command-line use.

## 19.4 Compatibility limitations

Different file systems and operating systems support different features.

Some file-system formats may not be fully supported by a particular Unix-like system.

---

# 20. Unix File System vs Windows File Organization

| Feature               | Unix-like systems                                             | Windows                                                                |
| --------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Directory hierarchy   | Starts from `/`                                               | Commonly uses drive roots such as `C:\`                                |
| Path separator        | `/`                                                           | Usually `\`                                                            |
| Root concept          | Root directory `/`                                            | Drive-specific roots                                                   |
| Inodes                | Used by many Unix file systems                                | NTFS uses different metadata structures                                |
| Permissions           | Commonly uses owner/group/other and additional ACL mechanisms | Commonly uses Windows access-control lists                             |
| Device representation | Commonly includes special files under `/dev`                  | Uses Windows device interfaces and object-management mechanisms        |
| File naming           | Case sensitivity depends on file system and configuration     | Commonly case-insensitive by default, with some case-sensitive support |

### Important distinction

Windows also has directories, metadata, permissions, and device interfaces. The difference is primarily in design, conventions, and implementation.

---

# 21. Real-World Examples

Let's test your understanding with practical situations.

## Example 1: Finding your personal notes

You save:

```text
/home/student/OS/notes.txt
```

What concepts are involved?

- `/` is the root directory.
- `home` is a directory.
- `student` is a home directory.
- `OS` is a subdirectory.
- `notes.txt` is an ordinary file.
- The pathname identifies its location.
- An inode stores its metadata and data-location information.

## Example 2: Connecting a USB device

You connect a USB drive.

You might see:

```text
/dev/sdb
```

This is an example of a possible block device name.

After the file system is mounted, the files may become accessible through a path such as:

```text
/media/student/USB
```

Concepts involved:

- Device files.
- Block devices.
- Mounting.
- Directories.
- File-system access.

## Example 3: Two programs communicating

Program A generates output.

Program B needs that output.

You execute:

```bash
ls | wc -l
```

The pipe transfers the first command's output to the second command.

Concept involved: Inter-process communication.

## Example 4: Creating a shortcut

You have:

```text
/home/student/project/report.txt
```

You create:

```bash
ln -s /home/student/project/report.txt report-shortcut.txt
```

The symbolic link gives you another path to access the original file.

Concept involved: Symbolic links.

## Example 5: Checking system information

You execute:

```bash
cat /proc/meminfo
```

You see memory information.

Concept involved: Virtual file systems and `/proc`.

## Example 6: Checking system logs

You execute:

```bash
ls /var/log
```

You see system log files.

Concept involved: `/var` and `/var/log`.

---

# 22. Interview Questions and Answers

## Beginner-level questions

### Q1. What is a Unix file system?

A Unix file system is a hierarchical structure that organizes and manages files and directories. It uses the root directory, inodes, pathnames, and permissions.

### Q2. What is the root directory?

The root directory is the topmost directory of the Unix file-system hierarchy, represented by `/`.

### Q3. What is an inode?

An inode is a data structure that stores file metadata and information needed to locate file data.

### Q4. Does an inode store a filename?

Normally, no. Directory entries associate filenames with inode numbers.

### Q5. What is a pathname?

A pathname is a sequence of directory names that identifies the location of a file or directory.

### Q6. What is the `/home` directory?

It normally contains users' personal home directories.

### Q7. What is the `/etc` directory?

It contains system-wide configuration files.

### Q8. What is the `/dev` directory?

It contains representations of device interfaces and pseudo-devices.

### Q9. What is the `/proc` directory?

It is a virtual file system that exposes process and kernel information on Linux.

### Q10. What is a symbolic link?

A symbolic link is a special file that stores a pathname pointing to another file or directory.

## Intermediate-level questions

### Q11. What are the six major types of Unix files?

1. Ordinary files.
2. Directories.
3. Special files.
4. Pipes.
5. Sockets.
6. Symbolic links.

### Q12. What is the difference between character and block special files?

Character special files provide character- or stream-oriented I/O, while block special files provide block-oriented I/O.

### Q13. What is a pipe?

A pipe is an inter-process communication mechanism that allows one process to send data to another.

### Q14. What is a named pipe?

A named pipe, also called a FIFO, is a pipe that has a pathname and can be opened by processes to communicate.

### Q15. What is a socket?

A socket is a communication endpoint used by processes to exchange information.

### Q16. What is the difference between a hard link and a symbolic link?

A hard link is another directory entry for the same inode. A symbolic link is a separate object that stores a pathname to another object.

### Q17. What is the difference between `/` and `/root`?

`/` is the root directory. `/root` is the home directory of the root user.

### Q18. What does `ls -l` display?

It displays detailed file information, including type, permissions, link count, owner, group, size, modification time, and name.

### Q19. What does `chmod` do?

It changes file or directory permissions.

### Q20. What does `ls -i` do?

It displays inode numbers.

## Advanced-level questions

### Q21. How does pathname resolution work?

The operating system starts from the appropriate starting directory and looks up each pathname component through directory entries until it resolves the target object.

### Q22. Can multiple filenames point to the same inode?

Yes. Hard links allow multiple directory entries to refer to the same inode.

### Q23. What happens when a symbolic link's target is deleted?

The symbolic link can remain, but it becomes a dangling link because its target can no longer be resolved.

### Q24. Can a symbolic link point to a directory?

Yes.

### Q25. Can a hard link normally cross file-system boundaries?

No. A hard link refers to an inode within the same file system.

### Q26. What is the difference between `/tmp` and `/var/tmp`?

`/tmp` is used for temporary data that may be cleaned relatively frequently. `/var/tmp` is intended for temporary data that should generally survive reboots.

### Q27. Why is Unix described as treating everything as a file?

Because Unix provides file-oriented interfaces for many resources, including ordinary data, devices, and communication channels. They are not all identical in behavior.

### Q28. What is the purpose of `/var/log`?

It stores system and application log files used for monitoring and troubleshooting.

### Q29. What is the difference between a pipe and a socket?

A traditional pipe is a simple communication channel, while a socket provides a more general communication endpoint and can support richer communication patterns.

### Q30. What happens when a process opens a file?

The operating system resolves the pathname, performs access checks, locates the relevant file-system object, and provides the process with a file descriptor if the operation succeeds.

---

# 23. Practical Exercises

Try these exercises without reading the answers first.

## Exercise 1: Identify the directory

Where would you normally find:

1. System configuration files?
2. User home directories?
3. Device representations?
4. System logs?
5. Temporary files?
6. Boot-related files?

## Exercise 2: Identify file types

What does each symbol represent?

```text
-
d
c
b
p
s
l
```

## Exercise 3: Understand inodes

Suppose:

```text
notes.txt  ---> Inode 105
backup.txt ---> Inode 105
```

Answer:

1. Are these two independent file objects?
2. Do they share the same inode?
3. If you modify the file through `notes.txt`, will the change be visible through `backup.txt`?

## Exercise 4: Understand symbolic links

Suppose:

```text
original.txt
shortcut.txt -> original.txt
```

What happens if:

1. You delete `shortcut.txt`?
2. You delete `original.txt`?
3. You modify `original.txt`?

## Exercise 5: Identify the communication mechanism

Which mechanism is suitable for each situation?

1. Connecting the output of `ls` to `wc`.
2. Communication between two local applications using a client-server design.
3. Connecting processes through a named FIFO.

## Exercise 6: Analyze a command

Explain:

```bash
who | wc -l
```

What is the role of each command and the pipe?

## Exercise 7: Understand permissions

What does this permission mean?

```text
-rwxr-xr--
```

Identify the permissions of:

1. Owner.
2. Group.
3. Others.

## Exercise 8: Explain pathname resolution

Explain how Unix accesses:

```text
/home/student/college/notes.txt
```

Include directories, directory entries, inodes, and data blocks.

## Exercise 9: Practical command challenge

Write commands to:

1. Create a directory called `Unix`.
2. Enter that directory.
3. Create `notes.txt`.
4. Display its inode number.
5. Create a symbolic link called `shortcut`.
6. Display detailed file information.

## Exercise 10: Teach someone else

Explain these concepts using your own examples:

- Root directory.
- Inode.
- Pathname.
- Ordinary file.
- Character special file.
- Block special file.
- Pipe.
- Socket.
- Symbolic link.

### Exercise answers

**1.**

1. `/etc`
2. `/home`
3. `/dev`
4. `/var/log`
5. `/tmp`
6. `/boot`

**2.**

| Symbol | Meaning                |
| ------ | ---------------------- |
| `-`    | Ordinary file          |
| `d`    | Directory              |
| `c`    | Character special file |
| `b`    | Block special file     |
| `p`    | Named pipe             |
| `s`    | Socket                 |
| `l`    | Symbolic link          |

**3.**

1. They are two names for the same underlying file object.
2. Yes.
3. Yes, because both names refer to the same inode and data.

**4.**

1. Only the symbolic link is removed.
2. The link becomes dangling.
3. The changed content can be accessed through the symbolic link.

**5.**

1. Pipe.
2. Unix domain socket.
3. Named pipe.

**6.**

`who` displays logged-in users. `|` transfers the output to `wc -l`, which counts the lines.

**7.**

- Owner: read, write, execute.
- Group: read, execute.
- Others: read.

**8.**

The operating system starts from `/`, looks up `home`, then `student`, then `college`, and finally `notes.txt`. The final directory entry identifies the inode, which contains metadata and information used to locate the file data.

**9.**

```bash
mkdir Unix
cd Unix
touch notes.txt
ls -i notes.txt
ln -s notes.txt shortcut
ls -l
```

**10.**

There is no single correct analogy. Your explanation is successful if you correctly describe the purpose and behavior of each concept.

---

# 24. Revision Cheat Sheet

This section is designed for quick revision before examinations.

| Concept                | One-line meaning                                           |
| ---------------------- | ---------------------------------------------------------- |
| Unix file system       | Hierarchical system for organizing and managing files      |
| Root directory         | Topmost directory represented by `/`                       |
| Inode                  | Stores file metadata and data-location information         |
| Pathname               | Identifies an object's location in the directory hierarchy |
| Directory              | Organizes filenames and references                         |
| `/bin`                 | Fundamental executable programs                            |
| `/boot`                | Boot-related files                                         |
| `/dev`                 | Device and pseudo-device representations                   |
| `/etc`                 | Configuration files                                        |
| `/home`                | User home directories                                      |
| `/lib`                 | Libraries and essential support files                      |
| `/media`               | Removable-media mount points                               |
| `/mnt`                 | Conventional mount points                                  |
| `/proc`                | Virtual process and kernel information                     |
| `/root`                | Root user's home directory                                 |
| `/tmp`                 | Temporary files                                            |
| `/usr`                 | Programs and shared resources                              |
| `/var`                 | Frequently changing data                                   |
| Ordinary file          | Stores normal content                                      |
| Directory file         | Organizes entries                                          |
| Character special file | Stream-oriented device interface                           |
| Block special file     | Block-oriented device interface                            |
| Pipe                   | Process communication channel                              |
| Socket                 | Communication endpoint                                     |
| Symbolic link          | Stores a pathname to another object                        |
| Hard link              | Another name for the same inode                            |
| `r`                    | Read                                                       |
| `w`                    | Write                                                      |
| `x`                    | Execute                                                    |
| `ls -l`                | Detailed file listing                                      |
| `ls -i`                | Display inode numbers                                      |
| `chmod`                | Change permissions                                         |
| `mkfifo`               | Create a named pipe                                        |

## Seven things you should never forget

1. `/` is the root directory, not `/root`.
2. Inodes store metadata, not ordinary filenames.
3. Directory entries associate names with inode numbers.
4. Unix has six commonly taught file types.
5. Character special files provide stream-oriented I/O; block special files provide block-oriented I/O.
6. Pipes and sockets enable process communication.
7. Symbolic links store pathnames, while hard links share an inode.

---

# 25. Explain Unix File System in 60 Seconds

Now imagine that someone asks you:

**"Can you explain the Unix file system?"**

Here is a good answer:

"The Unix file system is a hierarchical structure used to organize and manage files and directories.

Everything starts from the root directory, represented by `/`. Under the root, we have directories such as `/home`, `/etc`, `/bin`, `/dev`, `/usr`, and `/var`, each having a different responsibility.

Unix uses inodes to store file metadata and information about where file data is located. Directory entries associate filenames with inode numbers, while pathnames help locate files.

Unix traditionally identifies six major types of files: ordinary files, directories, special files, pipes, sockets, and symbolic links.

Special files are further divided into character and block special files. Pipes and sockets are used for communication between processes. Symbolic links provide alternative paths to other files or directories.

Unix also uses permissions to control access to files and directories.

In short, the Unix file system provides an organized and flexible way to manage files, devices, and communication resources."

---

# 26. Final Self-Assessment

Before moving to the next topic, check whether you can do these things without looking at the notes.

## Concept understanding

- [ ] I can define Unix file systems.
- [ ] I can explain the meaning of everything is a file.
- [ ] I can draw the Unix directory hierarchy.
- [ ] I can explain the difference between `/` and `/root`.
- [ ] I can explain all the important Unix directories.
- [ ] I can explain inodes using a real-life example.
- [ ] I can explain pathname resolution.
- [ ] I can distinguish absolute and relative paths.

## File types

- [ ] I can explain ordinary files.
- [ ] I can explain directories.
- [ ] I can explain special files.
- [ ] I can distinguish character and block devices.
- [ ] I can explain anonymous and named pipes.
- [ ] I can explain Unix domain sockets.
- [ ] I can explain symbolic links.
- [ ] I can distinguish hard links from symbolic links.

## Practical understanding

- [ ] I can use `pwd`, `ls`, `cd`, and `mkdir`.
- [ ] I can create files using `touch`.
- [ ] I can inspect inodes using `ls -i`.
- [ ] I can inspect metadata using `stat`.
- [ ] I can create symbolic links using `ln -s`.
- [ ] I can create named pipes using `mkfifo`.
- [ ] I can understand permissions using `ls -l`.
- [ ] I can change permissions using `chmod`.

## Teaching ability

- [ ] I can explain the complete Unix file-system hierarchy.
- [ ] I can draw all six file types.
- [ ] I can explain how a pathname resolves to an inode.
- [ ] I can explain how two processes communicate through a pipe.
- [ ] I can explain what happens when a symbolic link's target is deleted.

---

# Conclusion

You have now covered the major concepts of the Unix file system, from the root directory to inodes, special files, pipes, sockets, and symbolic links.

The best way to remember the entire topic is to think about these five questions:

1. **Where does everything start?** — Root directory (`/`).
2. **How are files organized?** — Directories.
3. **How does Unix identify and manage file objects?** — Inodes.
4. **How do we locate files?** — Pathnames.
5. **What different kinds of resources can Unix represent?** — Ordinary files, directories, special files, pipes, sockets, and symbolic links.

If you can answer these five questions confidently, you have understood the foundation of the Unix file system.

## Recommended next topics in Operating Systems

Study these topics in this order:

1. File-System Implementation.
2. File-System Mounting.
3. File Allocation Methods.
4. Free-Space Management.
5. Disk Management.
6. I/O Systems.
7. Protection and Security.

**Final advice:** Don't just read this document. Open a Linux terminal, execute the commands, observe the output, draw the diagrams, and explain the concepts aloud. Understanding becomes much stronger when you connect theory with actual system behavior.

**You should aim to understand every concept well enough to explain why it exists, how it works, and where it is used.**
