# File Systems in Operating Systems

> **Goal:** Understand file systems from zero, learn how an operating system stores and organizes files, master the important concepts for exams and interviews, and become confident enough to explain the entire topic in your own words.

## Table of Contents

1. [Introduction to File Systems](#1-introduction-to-file-systems)
2. [What Is a File?](#2-what-is-a-file)
3. [What Is a File System?](#3-what-is-a-file-system)
4. [How a File System Works](#4-how-a-file-system-works)
5. [Layers of a File System](#5-layers-of-a-file-system)
6. [Popular Types of File Systems](#6-popular-types-of-file-systems)
7. [File Names, Extensions, and Metadata](#7-file-names-extensions-and-metadata)
8. [File Operations: CRUD](#8-file-operations-crud)
9. [File Directories](#9-file-directories)
10. [Directory Structures](#10-directory-structures)
11. [File Allocation Methods](#11-file-allocation-methods)
12. [Disk Free-Space Management](#12-disk-free-space-management)
13. [File-System Mounting and Partitions](#13-file-system-mounting-and-partitions)
14. [File Permissions and Protection](#14-file-permissions-and-protection)
15. [File-System Reliability and Journaling](#15-file-system-reliability-and-journaling)
16. [File-System Performance](#16-file-system-performance)
17. [What Happens When You Save a File?](#17-what-happens-when-you-save-a-file)
18. [What Happens When You Delete a File?](#18-what-happens-when-you-delete-a-file)
19. [Important Differences and Comparison Tables](#19-important-differences-and-comparison-tables)
20. [Advantages and Challenges of File Systems](#20-advantages-and-challenges-of-file-systems)
21. [Common Misconceptions](#21-common-misconceptions)
22. [Real-World Examples](#22-real-world-examples)
23. [Interview and Exam Questions](#23-interview-and-exam-questions)
24. [Practice Exercises](#24-practice-exercises)
25. [Revision Cheat Sheet](#25-revision-cheat-sheet)
26. [Explain File Systems in 60 Seconds](#26-explain-file-systems-in-60-seconds)
27. [Final Self-Test](#27-final-self-test)

---

## 1. Introduction to File Systems

### 1.1 What problem does a file system solve?

Imagine that you have a cupboard containing 1,000 books.

If you throw every book into the cupboard without organizing anything, finding one particular book will be difficult.

Now imagine that you:

- Give each book a name.
- Put similar books into separate shelves.
- Maintain a list of where each book is located.
- Keep track of empty spaces.
- Decide who is allowed to access certain books.

Finding and managing books becomes much easier.

A computer faces a similar problem. It may store millions of documents, images, videos, applications, and other files.

The operating system needs a reliable way to organize all this information.

**That is why we need a file system.**

### 1.2 What is a file system?

A **file system** is a method and set of data structures used by an operating system to name, organize, store, retrieve, protect, and manage files on a storage device.

Examples of storage devices include:

- Hard disk drives (HDDs).
- Solid-state drives (SSDs).
- USB flash drives.
- Memory cards.
- Other supported storage devices.

### 1.3 A simple real-life analogy

| Computer concept | Everyday example |
|---|---|
| Storage device | A large cupboard |
| File system | The cupboard's organization system |
| File | A book or document |
| Directory | A folder or shelf |
| File name | The title written on a book |
| Metadata | Information describing the book |
| Disk block | A small storage space |
| Free-space manager | A record of empty spaces |
| File permissions | Rules about who may open a document |

**Remember:** A storage device provides the physical capacity. The file system organizes that capacity so the operating system can use it effectively.

### 1.4 Why is a file system necessary?

Without an appropriate file system, the operating system would need some other mechanism to manage raw storage.

A file system normally helps with:

1. **Organization:** Keeping related files together.
2. **Naming:** Giving files recognizable names.
3. **Retrieval:** Finding a file when requested.
4. **Space allocation:** Determining where file contents should be stored.
5. **Free-space management:** Tracking unused storage blocks.
6. **Protection:** Controlling who can access files.
7. **Reliability:** Helping maintain consistency after errors or unexpected shutdowns.
8. **Efficiency:** Reducing unnecessary work when reading and writing data.

### 1.5 First concept to memorize

> A file system is like a librarian for a computer's storage: it organizes files, remembers where their contents are stored, and helps the operating system retrieve and manage them.

---

## 2. What Is a File?

### 2.1 Definition

A **file** is a named collection of information stored and managed by an operating system.

For example:

- `notes.txt` contains text.
- `photo.jpg` contains image data.
- `song.mp3` contains audio data.
- `assignment.pdf` contains document data.
- `program.py` contains Python source code.

A file might contain text, numbers, images, sound, video, program instructions, or other kinds of data.

### 2.2 What is inside a file?

Suppose you create a file named `student.txt` containing:

```text
Name: Aarya
Subject: Operating Systems
Topic: File Systems
```

The file has two important aspects:

1. **Content:** The actual information written inside it.
2. **Metadata:** Information about the file, such as its size, timestamps, permissions, and other attributes supported by the file system.

Think of metadata as the label attached to a package. The package contains the actual item; the label describes the package.

### 2.3 Are files always stored in one continuous location?

No.

A file may occupy several storage blocks. Those blocks might be next to each other or spread across different locations on the storage device.

For example, imagine a file occupying blocks 10, 11, 12, and 13. It is stored contiguously.

Another file might occupy blocks 10, 27, 44, and 61. Its contents are stored in non-contiguous locations.

The file system maintains information that allows the operating system to locate the file's contents.

### 2.4 Do all files have extensions?

No.

An extension is a common way to indicate a file's type, particularly in systems such as Windows.

Examples:

- `report.pdf`
- `photo.png`
- `program.c`

However, files can have no extension, and an extension alone does not guarantee the actual content type. Some operating systems use other information to identify file types.

### Quick recap

A file is the named information you want to save. Its content is the information itself, while its metadata describes and helps manage that information.

---

## 3. What Is a File System?

Now connect the idea of a file with the system that manages it.

Imagine you save three files:

```text
notes.txt
photo.jpg
assignment.pdf
```

The operating system needs to know:

- What files exist?
- What are their names?
- Where are their contents stored?
- How large are they?
- Which directory contains each file?
- Which users can access them?
- Which storage blocks are still available?

The file system maintains the structures and rules that answer these questions.

### 3.1 File-system metadata

Metadata is information about files and the organization of storage.

Depending on the file system, metadata can include:

- File size.
- File type or type-related attributes.
- Creation, modification, or access timestamps.
- Ownership.
- Access permissions.
- Information locating the file's data.
- Directory relationships.
- Information about available storage space.

Different file systems store metadata differently.

### 3.2 The three big jobs

For an introductory understanding, group file-system responsibilities into three categories:

**Job 1: Organize**

- Manage file names.
- Maintain directories.
- Track file attributes.

**Job 2: Store and retrieve**

- Allocate storage blocks.
- Remember where file data lives.
- Read and write file contents.

**Job 3: Manage and protect**

- Track free storage space.
- Enforce supported access permissions.
- Maintain consistency and support recovery.

### 3.3 A simple example

Suppose you save a file called `os_notes.txt` inside a folder called `College`.

Its path might be:

```text
/home/student/College/os_notes.txt
```

The file system must support locating the file through its directory path and finding the data associated with it.

You do not need to remember the physical SSD addresses. You work with a name and a path, while the operating system handles the underlying details.

---

## 4. How a File System Works

Let's follow a file from a user's request to its stored contents.

### 4.1 The basic process

Suppose you open `notes.txt`.

1. You ask an application to open the file.
2. The application requests a file operation through the operating system.
3. The operating system resolves the file's path and checks the relevant access rules.
4. The file system looks up the file's metadata and storage information.
5. The operating system obtains the required data from storage, possibly using cached data if available.
6. The application receives the requested content.

The reverse happens when data must be written: the operating system processes the request and updates the appropriate file-system structures and stored data.

### 4.2 Conceptual diagram

```text
          USER
            |
            v
     Application
            |
            v
   Operating System
            |
            v
       File System
            |
            v
    Storage Driver
            |
            v
     HDD / SSD / USB
```

This is a simplified conceptual diagram. Real operating systems may use additional layers, caches, device abstractions, and different internal pathways.

### 4.3 Why don't applications manage physical blocks themselves?

Applications generally want to work with useful concepts such as filenames, directories, and file contents.

They should not need to manage every physical storage location or coordinate all the low-level details of a particular storage device.

The operating system and file system provide that abstraction.

**Key principle:** Applications work with files; the file system manages how those files are represented and organized on storage.

---

## 5. Layers of a File System

A file system is often explained using layers. This is a conceptual model, not a universal architecture that every operating system implements identically.

### 5.1 User application

This is the software that requests file operations.

Examples:

- A text editor saving a document.
- A browser downloading a file.
- A media player opening a video.
- An application deleting a temporary file.

The application usually uses operating-system interfaces instead of directly managing disk blocks.

### 5.2 Logical file system

The logical file-system layer deals with the organization and management of files.

Its responsibilities can include:

- Managing directories and names.
- Handling file metadata.
- Maintaining file-system-specific structures.
- Enforcing access-control rules.
- Mapping file-system objects to their associated information.

**Analogy:** The librarian's catalog records a book's title and where it belongs.

### 5.3 Virtual File System (VFS)

A **Virtual File System** is an operating-system abstraction that can provide a common interface for different file-system implementations.

For example, an operating system might access files on different volumes using the same general file-operation interface, even if the underlying file systems differ.

The VFS does not replace every file system. It helps applications and kernel components interact with different implementations through a common framework.

**Analogy:** A universal service counter that accepts requests for several different storage systems.

### 5.4 Physical file-system and storage mechanisms

This part of the conceptual model concerns how file-system structures are translated into storage operations.

Responsibilities may include:

- Locating data blocks.
- Managing storage allocation.
- Reading and writing data and metadata.
- Interacting with lower-level storage mechanisms.

The exact division between logical and physical operations varies across operating systems and file systems.

### 5.5 Partitions and storage devices

A storage device can be divided into partitions or volumes. Different partitions can hold different file systems, or some areas can be used for other purposes.

For example:

```text
SSD
 |
 +-- Partition 1: Operating system
 |
 +-- Partition 2: Personal files
 |
 +-- Partition 3: Recovery or other use
```

This is only an example. Actual partition layouts vary, and a partition is not the same thing as a file-system layer.

### 5.6 Layer summary

| Layer or concept | Main responsibility |
|---|---|
| User application | Requests file operations |
| Logical file-system functions | Names, metadata, directories, and access rules |
| VFS | Common interface for supported file-system implementations |
| File-system storage mechanisms | Manage allocation and locate data |
| Device and driver mechanisms | Communicate with storage hardware |
| Partition or volume | Defines a storage region that may contain a file system |

### Exam tip

If asked to explain the layers of a file system, describe the path from an application request through operating-system file handling to the storage device. Mention that the precise internal layering differs between systems.

---

## 6. Popular Types of File Systems

Different file systems have different design goals, compatibility requirements, and capabilities.

### 6.1 FAT — File Allocation Table

FAT is a family of file systems that includes FAT12, FAT16, and FAT32.

Common uses have included USB drives, memory cards, and removable storage.

**How it works conceptually:**

FAT maintains a table that helps track how the clusters belonging to a file are connected.

Imagine that a file occupies clusters 4, 9, and 15. The allocation table can record the sequence needed to locate the file's data.

**Advantages:**

- Broad compatibility.
- Relatively simple design.
- Useful for supported removable-storage scenarios.

**Limitations:**

- FAT32 does not support individual files larger than 4 GiB minus 1 byte.
- It lacks many advanced features available in modern file systems, such as built-in file permissions and journaling in the usual FAT implementations.

### 6.2 NTFS — New Technology File System

NTFS is a major file system used by Windows.

**Important features include:**

- Access-control permissions.
- Journaling support for file-system metadata.
- Support for large files and volumes.
- Compression and encryption capabilities, depending on the Windows features and configuration used.
- File metadata organized through structures including the Master File Table (MFT).

**Example:** On a Windows computer, an NTFS volume can support separate access permissions for different users.

### 6.3 ext — Extended File System family

Linux commonly uses the ext family, especially ext4.

Examples include:

- ext2.
- ext3.
- ext4.

Features vary by version. ext3 and ext4 include journaling support, and ext4 provides capabilities such as extents, which can represent contiguous ranges of blocks efficiently.

**Example:** A Linux installation might use ext4 for its main storage volume.

### 6.4 HFS and HFS+

HFS is an older Apple file-system design, with HFS+ being its later development.

These are historically important, but they have largely been superseded by APFS on modern Apple systems.

### 6.5 APFS — Apple File System

APFS is Apple's modern file system for supported Apple operating systems and devices.

Its design includes features such as:

- Copy-on-write metadata updates.
- Space sharing between supported volumes in a container.
- Snapshots.
- Encryption capabilities.
- Cloning support.

Availability and specific capabilities depend on the operating system, device, and configuration.

### 6.6 Comparison table

| Feature | FAT32 | NTFS | ext4 | APFS |
|---|---|---|---|---|
| Common association | Removable storage | Windows | Linux | Apple platforms |
| Broad compatibility | Very high | Best with Windows-oriented support | Best with Linux-oriented support | Best with Apple-oriented support |
| Journaling | No | Yes | Yes | Uses copy-on-write design rather than traditional journaling |
| Individual-file size | Maximum just under 4 GiB | Supports large files | Supports large files | Supports large files |
| Permissions | Limited | Rich access control | Rich permissions | Rich permissions |
| Notable characteristic | Compatibility | Windows features and metadata | Linux support and extents | Snapshots, cloning, space sharing |

Compatibility is not simply yes or no: other operating systems may read or write some of these file systems with varying levels of support.

### 6.7 Which file system is best?

There is no universal winner.

- Choose FAT32 when compatibility with older or limited devices is important and its limitations are acceptable.
- NTFS is a common choice for Windows system and data volumes.
- ext4 is a widely used choice for Linux installations.
- APFS is the standard choice for many modern Apple storage configurations.

The correct choice depends on the device, operating system, workload, compatibility needs, and reliability requirements.

---

## 7. File Names, Extensions, and Metadata

### 7.1 File name

A file name helps users identify a file.

Examples:

```text
notes.txt
profile.jpg
project.zip
```

The rules governing valid names depend on the operating system and file system.

### 7.2 File extension

An extension is the suffix that follows a period in many file names.

| Extension | Common association |
|---|---|
| `.txt` | Plain text |
| `.pdf` | Portable Document Format |
| `.jpg` | Image |
| `.mp3` | Audio |
| `.mp4` | Video container |
| `.py` | Python source code |
| `.zip` | Archive |

An extension is a naming convention. Renaming `photo.jpg` to `photo.txt` does not automatically transform the image data into text.

### 7.3 Metadata

Consider the file `notes.txt`.

Its metadata might describe:

- Name: `notes.txt`.
- Size: 2,048 bytes.
- Owner: a particular user.
- Permissions: who may read or modify it.
- Modification time: when the content was last changed.
- Storage information: where the data can be found.

These values are illustrative; actual metadata fields depend on the system.

### 7.4 File name versus metadata

| File name | Metadata |
|---|---|
| Helps identify the file | Describes the file |
| Example: `notes.txt` | Example: size = 2,048 bytes |
| Usually visible in a file browser | Some fields may be hidden from ordinary users |

**Memory trick:** The name tells you *which file*; metadata tells the system *about that file*.

---

## 8. File Operations: CRUD

CRUD stands for Create, Read, Update, and Delete.

These are common data-management operations that also provide a useful way to understand file handling.

### 8.1 Create

Creating a file establishes a new file-system object or directory entry, with associated metadata as required.

Example:

```text
Create: homework.txt
```

The system must establish the file and make it accessible by its name. Storage for the actual content may be allocated immediately or later, depending on the file system and operation.

### 8.2 Read

Reading retrieves file content.

Example:

```text
Read: homework.txt
```

A text editor might request the file's contents so it can display them.

### 8.3 Update

Updating changes existing data or metadata.

Example:

```text
Update: homework.txt
```

You add a paragraph and save the file.

The file system and operating system handle the resulting writes and metadata updates.

### 8.4 Delete

Deleting removes a file-system name or reference and eventually allows the associated storage to be reclaimed when no longer needed.

Example:

```text
Delete: homework.txt
```

Deletion does not necessarily mean every byte of the previous contents is immediately erased from the physical device.

### CRUD summary

| Operation | Meaning | Example |
|---|---|---|
| Create | Establish a file | Create `notes.txt` |
| Read | Retrieve contents | Open `notes.txt` |
| Update | Change contents or metadata | Edit `notes.txt` |
| Delete | Remove the file reference | Delete `notes.txt` |

**Important distinction:** CRUD is a general data-management concept. File systems support file operations, but not every file operation maps to one unique low-level disk operation.

---

## 9. File Directories

### 9.1 What is a directory?

A **directory** is a file-system structure that organizes names and references to files and, commonly, other directories.

In everyday language, we call a directory a folder.

Example:

```text
College/
    notes.txt
    timetable.pdf
    assignment.docx
```

Here, `College` is a directory containing references to three files.

### 9.2 What information does a directory maintain?

A directory commonly associates names with file-system objects.

Depending on the system, directory-related information can help the operating system locate:

- Files.
- Subdirectories.
- File identifiers or metadata records.

The exact internal representation differs by file system.

### 9.3 Is a directory itself a file?

This needs a little care.

Some file systems represent directories as special kinds of file-system objects, and their internal structures may be stored in file-like ways. However, directories are not ordinary user-content files, and their implementation differs across systems.

For learning purposes, the safest definition is:

**A directory is a structure used to organize names and references to file-system objects.**

### 9.4 Advantages of directories

**Efficiency:** A structured directory hierarchy helps users and applications locate files.

**Naming:** Different directories can contain files with the same name, as long as their full paths differ and the file system's naming rules permit it.

**Grouping:** Related items can be kept together.

**Administration:** Permissions and organizational policies can often be applied at directory level, subject to the operating system's rules.

### 9.5 Example

```text
College/
    OperatingSystems/
        notes.txt
        deadlocks.pdf
    Mathematics/
        algebra.pdf
        calculus.pdf
```

This structure makes it easy to find operating-system notes without mixing them with mathematics materials.

## 10. Directory Structures

Operating systems can organize directories in different ways.

### 10.1 Single-Level Directory

All files are stored in one directory.

```text
Root Directory
   |
   +-- notes.txt
   +-- photo.jpg
   +-- program.py
   +-- homework.pdf
```

**Advantages:**
- Very simple to understand.
- Easy to implement.

**Disadvantages:**
- Different users may want to create files with the same name.
- Finding a particular file becomes difficult when many files exist.
- There is no natural way to group files into subdirectories.

**Real-life analogy:** Every book in a library is placed on one enormous table without separate shelves.

### 10.2 Two-Level Directory

The system maintains a separate directory for each user.

```text
Root Directory
   |
   +-- User A
   |     +-- notes.txt
   |     +-- photo.jpg
   |
   +-- User B
         +-- notes.txt
         +-- program.py
```

Notice that both users can have a file called `notes.txt` because the files belong to different directories.

**Advantages:**
- Reduces naming conflicts between users.
- Separates users' files.
- Makes basic file organization easier.

**Disadvantages:**
- Traditional two-level structures offer limited grouping within each user's directory.
- Organizing large collections of files can still be difficult.

### 10.3 Tree-Structured Directory

Directories can contain subdirectories, producing a tree.

```text
Root
 |
 +-- Home
 |    |
 |    +-- Student
 |         |
 |         +-- College
 |         |    +-- notes.txt
 |         |    +-- assignment.pdf
 |         |
 |         +-- Personal
 |              +-- photo.jpg
 |
 +-- System
      +-- configuration files
```

**Advantages:**
- Supports logical grouping.
- Makes large collections easier to navigate.
- Allows files with the same name in different directories.
- Supports absolute and relative paths.

**Disadvantages:**
- Users must understand directory paths.
- Very deep or poorly organized trees can become difficult to navigate.

### 10.4 Absolute path

An absolute path identifies a location from the file-system root or another defined namespace root.

Example on Linux:

```text
/home/student/College/notes.txt
```

The path describes the complete route from `/` to the file.

### 10.5 Relative path

A relative path identifies a location in relation to the current working directory or another specified starting point.

Suppose the current directory is:

```text
/home/student/College
```

Then the relative path to the file is:

```text
notes.txt
```

A relative path can also navigate into subdirectories:

```text
OperatingSystems/notes.txt
```

Or move to a parent directory:

```text
../Personal/photo.jpg
```

The meaning of a relative path depends on the starting directory.

### 10.6 Other directory structures

Some operating systems and file systems also support more complex structures, including acyclic graphs with shared references and, in certain models, general graphs.

These can allow files or directories to be reachable through multiple paths, but they introduce additional concerns such as reference management and cycles.

### Comparison

| Structure | Main idea | Best understood as |
|---|---|---|
| Single-level | One directory for all files | One big table |
| Two-level | One directory per user | Separate tables |
| Tree-structured | Directories contain subdirectories | A family tree |
| Graph-based | Objects may be shared or linked | A network of connected folders |

**Exam memory trick:** Single-level = one directory; two-level = separate user directories; tree = parent-child hierarchy.

---

## 11. File Allocation Methods

This is one of the most important topics in file systems.

### 11.1 What is file allocation?

File allocation is the method a file system uses to assign storage blocks to files and keep track of where those blocks are located.

First, understand three terms.

- **Block:** A unit of storage allocation used by a file system.
- **Allocation:** Assigning storage blocks to a file.
- **Fragmentation:** A situation where storage is divided into pieces in ways that can affect space usage or performance.

Imagine a storage device divided into numbered boxes:

```text
Block numbers:
[0] [1] [2] [3] [4] [5] [6] [7] [8] [9]
```

A file must occupy one or more of these blocks. The allocation method determines how they are assigned and tracked.

The three classic methods are:

1. Contiguous allocation.
2. Linked allocation.
3. Indexed allocation.

### 11.2 Contiguous allocation

In contiguous allocation, a file occupies consecutive storage blocks.

Suppose a file needs four blocks.

```text
Block:
[0] [1] [2] [3] [4] [5] [6] [7]

File A:
[ ] [A] [A] [A] [A] [ ] [ ] [ ]
```

File A occupies blocks 1, 2, 3, and 4.

The file system can represent this allocation using a starting block and a length.

For example:

```text
Starting block = 1
Number of blocks = 4
```

The blocks are consecutive, so the location of each block can be calculated directly.

**Advantages:**
- Simple to represent.
- Efficient sequential access.
- Efficient direct access because block locations can be calculated.
- Low metadata overhead for the basic allocation representation.

**Disadvantages:**
- Finding a sufficiently large consecutive free region can be difficult.
- External fragmentation can develop.
- Growing a file may be difficult if the following blocks are occupied.
- Moving or reorganizing files may be necessary in some implementations.

**Real-life analogy:** You need four consecutive seats in a cinema. Four empty seats scattered throughout the room are not enough.

### 11.3 External fragmentation

External fragmentation occurs when free space is divided into separated regions.

Imagine these blocks:

```text
[A] [ ] [B] [ ] [C] [ ] [D] [ ]
```

There are four free blocks, but none are consecutive.

A file needing four consecutive blocks cannot fit into this layout, even though four blocks are free.

This is a classic problem associated with contiguous allocation.

### 11.4 Linked allocation

In linked allocation, a file's blocks can be scattered across the storage device. Each block contains, or is associated with, information identifying the next block in the file.

Suppose a file occupies blocks 2, 8, 5, and 12.

```text
File A

[Block 2] --> [Block 8] --> [Block 5] --> [Block 12] --> END
```

The blocks do not need to be consecutive.

The file system follows the links to read the file's contents in order.

**Advantages:**
- Blocks can be allocated wherever suitable free space is available.
- Files can grow without needing one large contiguous region.
- Avoids the classic external-fragmentation problem of requiring consecutive file blocks.

**Disadvantages:**
- Sequential traversal is needed to reach later blocks in a basic implementation.
- Direct access is less efficient than in contiguous allocation because the system may need to follow links.
- Pointers consume storage space.
- Damaged pointers can disrupt access to later parts of a file.

**Real-life analogy:** A treasure hunt where each location tells you where to find the next clue.

### 11.5 FAT and linked allocation

The File Allocation Table used by FAT file systems is related to linked allocation.

Rather than storing every next-block pointer directly inside the user-data blocks, FAT maintains allocation information in a separate table.

A simplified example:

| Cluster | Next cluster |
|---|---|
| 2 | 8 |
| 8 | 5 |
| 5 | 12 |
| 12 | End of file |

To follow the file, the system follows the allocation-table entries.

Actual FAT structures use reserved values and other details that this simplified table omits.

### 11.6 Indexed allocation

In indexed allocation, a file has an index structure containing references to the blocks that hold its data.

For example:

```text
          Index for File A
          +-------------+
          | Block 2     |
          | Block 8     |
          | Block 5     |
          | Block 12    |
          +-------------+
             |  |  |  |
             v  v  v  v
            Data blocks
```

The index tells the file system where the file's data blocks are located.

**Advantages:**
- Supports direct access to blocks through the index.
- Data blocks need not be consecutive.
- Avoids the need to follow a chain through all preceding data blocks just to locate a later block.

**Disadvantages:**
- Index structures consume space.
- Very small files may not benefit from the extra index overhead.
- Very large files may require multiple index blocks or more complex structures.
- The index must be maintained correctly.

**Real-life analogy:** An index at the back of a textbook tells you which page contains a topic.

### 11.7 Comparison of the three methods

| Feature | Contiguous | Linked | Indexed |
|---|---|---|---|
| Block placement | Consecutive | Can be scattered | Can be scattered |
| Sequential access | Very good | Good | Good |
| Direct access | Very good | Poor in the basic linked design | Good, subject to index structure |
| External fragmentation | Possible | Not caused by the need for consecutive file blocks | Not caused by the need for consecutive file blocks |
| Metadata overhead | Low in the basic model | Link information | Index information |
| File growth | Can be difficult | Generally flexible | Generally flexible, subject to index capacity |

### 11.8 A numerical example

Suppose a file requires five blocks.

The available free blocks are:

```text
[2] [7] [9] [14] [20]
```

All five blocks are free, but they are scattered.

- **Contiguous allocation:** Cannot place the entire file in these five locations because they are not consecutive.
- **Linked allocation:** Can connect the blocks in the required order.
- **Indexed allocation:** Can record all five block addresses in the file's index.

This example demonstrates why allocation methods matter.

### 11.9 What do modern file systems use?

Modern file systems do not necessarily implement only one of these classic textbook methods.

For example, extent-based allocation represents groups of consecutive blocks as ranges. A file can be described using several extents, reducing the number of individual block references needed for files with large consecutive regions.

The important lesson is to understand the three classic methods first, then learn how real file systems combine or extend their ideas.

---

## 12. Disk Free-Space Management

### 12.1 What is free-space management?

Free-space management is the process of tracking which storage blocks are available for future allocations.

Imagine a hotel with 100 rooms.

The hotel must know which rooms are occupied and which are vacant. Otherwise, it might assign an already occupied room to a new guest.

A file system faces the same problem with storage blocks.

It must distinguish between:

- Allocated blocks, which are in use.
- Free blocks, which are available for allocation.

### 12.2 Method 1: Bitmaps

A bitmap uses one bit for each allocation unit being tracked.

For a simple convention:

- `0` = free.
- `1` = allocated.

The exact convention can differ between implementations.

Suppose eight blocks have this bitmap:

```text
Block:    0 1 2 3 4 5 6 7
Bitmap:   1 0 1 1 0 0 1 0
```

Here:

- Blocks 0, 2, 3, and 6 are allocated.
- Blocks 1, 4, 5, and 7 are free.

The operating system can use this information to locate available blocks.

**Advantages:**
- Compact representation.
- Can be efficient for finding free blocks or runs of free blocks.
- Easy to calculate the space needed for the bitmap.

**Disadvantages:**
- The bitmap itself requires storage.
- Large storage systems can have large bitmaps, although the overhead is generally manageable.
- Searching for free space can take time, depending on implementation and workload.

### 12.3 How much space does a bitmap require?

Suppose a storage system tracks 1,000,000 allocation units using one bit per unit.

The bitmap requires:

\[
1{,}000{,}000 \text{ bits}
\]

There are eight bits in a byte.

\[
\frac{1{,}000{,}000}{8}=125{,}000 \text{ bytes}
\]

That is approximately 125 kB using decimal units, excluding any additional metadata or alignment overhead.

**Important:** The bitmap is much smaller than one full byte per block because each entry uses only one bit.

### 12.4 Method 2: Free list

A free list maintains references to available blocks.

For example:

```text
Free-list head
      |
      v
    [3] --> [7] --> [11] --> [19] --> END
```

The list identifies free blocks that can be allocated.

**Advantages:**
- Conceptually simple.
- Can locate a free block without scanning a bitmap.

**Disadvantages:**
- Finding a suitable run of consecutive blocks may be difficult.
- Traversing a long list can be slow.
- List management consumes resources.

### 12.5 Method 3: Linked free-block list

A linked free-block list is a particular way to implement a free list: each free block, or associated metadata, points to the next free block.

For example:

```text
Block 5 --> Block 10 --> Block 17 --> Block 24
```

This approach allows the system to traverse the available blocks.

However, it is not always efficient when the system needs to find large consecutive runs of free space.

### 12.6 Method 4: Grouping

In grouping, one free block contains the addresses of several free blocks. The last entry can point to another block containing more free-block addresses.

Example:

```text
Free-space group 1
[4, 8, 12, 16, pointer to group 2]
                          |
                          v
Free-space group 2
[21, 25, 31, 38, END]
```

Grouping can reduce the number of operations needed to find multiple free blocks compared with following a single link for every block.

### 12.7 Method 5: Counting

Counting takes advantage of consecutive free blocks.

Instead of recording every block individually, the system can record:

- The starting block.
- The number of consecutive free blocks.

Example:

```text
Starting block = 40
Count = 6
```

This represents free blocks 40, 41, 42, 43, 44, and 45.

**Advantages:**
- Efficient representation when free space contains long consecutive runs.
- Can reduce metadata overhead.

**Disadvantages:**
- Less effective when free space is highly fragmented.
- The system must update the ranges when blocks become allocated or free.

### 12.8 What about boundary tags?

Boundary tags are commonly associated with memory allocators that manage free and allocated regions of a process's heap. They help identify neighboring regions and support operations such as merging adjacent free regions.

They are **not one of the standard core disk free-space management techniques usually taught in introductory operating-system file-system chapters**. For disk free-space management, focus first on bitmaps, free lists, grouping, and counting.

### 12.9 Comparison

| Method | Basic idea | Main strength |
|---|---|---|
| Bitmap | One bit per allocation unit | Compact tracking |
| Free list | Maintain references to free blocks | Simple management |
| Linked free-block list | Link available blocks together | Simple traversal |
| Grouping | Store several free-block addresses together | Efficiently find multiple free blocks |
| Counting | Store a start block and run length | Efficient for consecutive free regions |

### 12.10 File allocation versus free-space management

These concepts are related but not identical.

| File allocation | Free-space management |
|---|---|
| Determines which blocks belong to a file | Determines which blocks are available |
| Answers: "Where is this file stored?" | Answers: "Where can new data be stored?" |
| Examples: contiguous, linked, indexed | Examples: bitmap, linked free list, grouping, counting |

**Memory trick:** Allocation manages the blocks a file uses. Free-space management tracks the blocks nobody is using.

---

## 13. File-System Mounting and Partitions

### 13.1 What is a partition?

A partition is a defined region of a storage device.

A single SSD might contain several partitions, each used for a different purpose.

```text
SSD
 |
 +-- Partition A
 |      Operating system
 |
 +-- Partition B
 |      Personal files
 |
 +-- Partition C
        Recovery data
```

Partitions and file systems are different concepts. A partition can contain a file system, but the two terms are not interchangeable.

### 13.2 What is mounting?

Mounting makes a file system accessible through a directory in an operating system's namespace.

For example, on Linux, a USB drive might be mounted at:

```text
/media/student/USB
```

After mounting, the files on that drive can be accessed through that path, subject to permissions and other restrictions.

On other operating systems, mounting may be presented differently to the user. Windows commonly exposes volumes through drive letters, such as `D:\`.

### 13.3 What happens when you connect a USB drive?

A simplified sequence is:

1. The operating system detects the storage device.
2. It identifies the available storage layout and relevant volumes.
3. It determines whether it can recognize the file system.
4. The volume may be mounted automatically or manually.
5. The user can access its files if the volume is accessible and permissions allow it.

An unsupported, corrupted, encrypted, or unformatted volume may not be accessible through the normal file-management interface.

---

## 14. File Permissions and Protection

A file system may support access controls that determine who can read, modify, execute, or otherwise access files.

### 14.1 A simple example

Imagine a file named `private_notes.txt`.

You may want:

- Yourself to read and modify it.
- Other users to have no access.
- A group to read it without modifying it.

File permissions and ownership mechanisms can help enforce such rules.

### 14.2 Common permission categories

On Unix-like systems, permissions are commonly described in terms of:

- **Read (`r`):** Read file contents.
- **Write (`w`):** Modify file contents or perform the relevant permitted operations.
- **Execute (`x`):** Execute a file when appropriate, or traverse a directory.

For directories, read, write, and execute have directory-specific meanings. For example, execute permission on a directory generally permits traversal when other required permissions are satisfied.

Windows and other operating systems may use different access-control models.

### 14.3 Why are permissions important?

Permissions help with:

- Protecting private information.
- Preventing unauthorized changes.
- Separating user accounts.
- Supporting secure multi-user systems.
- Limiting access to sensitive system files.

**Important:** A file system can store or support access-control information, but actual access enforcement involves the operating system and its security mechanisms.

---

## 15. File-System Reliability and Journaling

### 15.1 What can go wrong?

Imagine that your computer loses power while saving a file.

Some data or metadata updates might have completed while others have not. Depending on the file system and the timing of writes, the storage may be left in an inconsistent state.

A file system must therefore consider reliability and recovery.

### 15.2 What is journaling?

A journaling file system records selected intended changes in a journal or log before, or as part of, applying those changes to the main file-system structures.

After a crash, the system can use the journal to help recover a consistent state.

A simplified sequence is:

```text
1. Prepare a metadata change
             |
             v
2. Record the change in the journal
             |
             v
3. Apply the change to file-system metadata
             |
             v
4. Complete the transaction
```

Actual journal protocols vary between file systems.

### 15.3 Why is journaling useful?

Journaling can:

- Reduce the chance of structural metadata inconsistency after a crash.
- Make recovery faster.
- Help restore a consistent file-system state.

However, journaling does not automatically guarantee that the latest user data is preserved. Some file systems journal metadata rather than all file contents, and durability depends on the implementation, configuration, and storage behavior.

### 15.4 Journaling versus backup

These are different.

| Journaling | Backup |
|---|---|
| Helps recover file-system consistency | Preserves a recoverable copy of data |
| Useful after crashes or interrupted updates | Useful after deletion, corruption, device failure, or other data loss |
| Does not necessarily preserve every latest file change | Can restore earlier copies when properly configured |

**Remember:** Journaling is not a substitute for backups.

---

## 16. File-System Performance

A file system must not only store data correctly; it must also retrieve and update it efficiently.

### 16.1 Factors that affect performance

- Storage-device latency and bandwidth.
- Block size and allocation strategy.
- File fragmentation.
- Directory lookup efficiency.
- Metadata overhead.
- Caching.
- Workload patterns.
- The number and type of storage operations.

### 16.2 Fragmentation

Fragmentation describes situations where storage is divided into separate pieces in a way that affects allocation or access.

Two useful distinctions are:

**External fragmentation:** Free space exists in separated regions, making it difficult to allocate one large contiguous region.

**File fragmentation:** A file's data is spread across multiple non-contiguous storage regions.

They are related concepts but are not identical.

Fragmentation may be more noticeable on HDDs because moving the disk head between distant locations takes time. SSDs do not have the same mechanical seek delays, although allocation patterns can still affect performance, metadata overhead, and other system behavior.

### 16.3 Caching

Operating systems may cache file data and metadata in memory.

If a requested file's data is already available in a suitable cache, the system may not need to read it from the storage device again.

**Analogy:** Keeping a frequently used book on your desk rather than walking to the cupboard every time.

Caching can improve performance, but cached writes and persistence require careful handling to maintain durability guarantees.

---

## 17. What Happens When You Save a File?

Let's understand a complete real-world example.

Suppose you create a text file called:

```text
OS_notes.txt
```

You type:

```text
A file system manages files and directories.
```

Then you click **Save**.

### Step-by-step process

**Step 1: The application requests a save.**

The text editor asks the operating system to create or update the file.

**Step 2: The operating system resolves the path.**

It determines which directory should contain the file and checks the relevant access rules.

**Step 3: The file system creates or updates metadata.**

The file's name, attributes, and storage information are established or updated as necessary.

**Step 4: Storage is allocated.**

The file system obtains the required storage space, which may happen incrementally or be deferred.

**Step 5: The content is written.**

The operating system and storage stack handle writing the data. Data may pass through memory caches before reaching persistent storage.

**Step 6: Metadata and allocation information are maintained.**

The file system records the information necessary to locate the data and track used space.

**Step 7: The save operation completes.**

The application reports success according to the guarantees provided by the operation. A successful ordinary save does not always mean every byte has already been physically persisted; explicit synchronization and storage guarantees matter when durability is critical.

### The important insight

When you save a file, the computer does much more than put words into a location. It manages names, metadata, allocation, access rules, and storage operations.

---

## 18. What Happens When You Delete a File?

Suppose you delete:

```text
OS_notes.txt
```

### Step-by-step process

**Step 1: A deletion request is made.**

The user or application asks the operating system to remove the file.

**Step 2: The operating system checks the relevant rules.**

The operation must satisfy the applicable permissions and file-system semantics.

**Step 3: The file-system reference is removed.**

The directory entry or equivalent reference is removed or changed.

**Step 4: Storage is reclaimed when possible.**

The file's storage can be returned to the free-space pool when it is no longer needed.

**Step 5: The freed blocks can be reused.**

Future file writes may allocate the blocks again.

### Does deleting a file erase every byte immediately?

Not necessarily.

Deleting a file commonly removes its accessible reference and marks its storage as available for reuse. The previous content may remain on the device until overwritten or otherwise erased.

Open files, snapshots, hard links, journaling, and other file-system features can affect exactly when storage is reclaimed.

### What about the Recycle Bin?

A desktop environment may move a file into a trash or recycle-bin area rather than immediately performing permanent deletion.

That behavior is implemented through the operating system or desktop environment and its storage conventions. It is not a universal property of every file system.

---

## 19. Important Differences and Comparison Tables

### 19.1 File versus directory

| File | Directory |
|---|---|
| Holds user data or other file content | Organizes names and references to file-system objects |
| Example: `notes.txt` | Example: `College/` |
| May have a size representing its content | Has directory-specific structures and metadata |

### 19.2 File system versus storage device

| File system | Storage device |
|---|---|
| Organizes and manages files | Provides physical or virtual storage capacity |
| Defines structures for names, allocation, and metadata | Examples include SSDs and HDDs |
| Example: NTFS | Example: a particular SSD |

### 19.3 File allocation versus free-space management

| File allocation | Free-space management |
|---|---|
| Tracks blocks associated with files | Tracks available blocks |
| Determines how file data is placed and located | Helps select space for new allocations |
| Examples: contiguous, linked, indexed | Examples: bitmap, free list, counting |

### 19.4 FAT versus NTFS

| FAT32 | NTFS |
|---|---|
| Broad compatibility with supported devices | Richer Windows-oriented features |
| Individual files cannot exceed the FAT32 size limit | Supports much larger files |
| No built-in journaling in standard FAT32 | Journaling support |
| Limited built-in permission features | Supports detailed access control |

### 19.5 Absolute versus relative paths

| Absolute path | Relative path |
|---|---|
| Starts from a defined root or namespace root | Starts from a specified working location |
| Describes the full route | Describes the route from the current starting point |
| Example: `/home/student/notes.txt` | Example: `notes.txt` |

### 19.6 Contiguous versus linked versus indexed allocation

| Property | Contiguous | Linked | Indexed |
|---|---|---|---|
| Consecutive blocks required | Yes | No | No |
| Sequential access | Excellent | Good | Good |
| Direct access | Excellent | Less efficient in basic form | Good |
| Metadata | Start and length | Links | Index |
| External fragmentation | Possible | Avoids the need for contiguous file space | Avoids the need for contiguous file space |

### 19.7 Journaling versus copy-on-write

| Journaling | Copy-on-write |
|---|---|
| Records selected intended changes in a log | Writes changed data or metadata to new locations rather than overwriting the old versions in place |
| Helps recover consistency after interrupted updates | Helps maintain consistency through carefully ordered updates and reference changes |
| Used by file systems such as NTFS and ext4 | Used in designs such as APFS and other copy-on-write file systems |

These are simplified descriptions. Specific file systems can use additional mechanisms, and their guarantees differ.

---

## 20. Advantages and Challenges of File Systems

### Advantages

1. **Organization:** Files can be grouped into directories.
2. **Retrieval:** Names and directory paths help locate files.
3. **Space management:** Allocation structures help assign available storage.
4. **Protection:** Supported permissions and access-control rules protect information.
5. **Reliability:** Some file systems provide journaling, snapshots, or other recovery features.
6. **Efficiency:** Metadata, caching, and allocation strategies can improve performance.
7. **Abstraction:** Applications can work with files without managing physical device locations.

### Challenges

1. **Fragmentation:** Allocation patterns can make storage management less efficient.
2. **Metadata overhead:** File-system structures consume space.
3. **Corruption:** Interrupted operations, hardware faults, and software bugs can damage data or metadata.
4. **Compatibility:** Different operating systems support different file systems and features.
5. **Security:** Incorrect permissions or configuration can expose information.
6. **Recovery complexity:** Repairing damaged structures or recovering deleted data can be difficult.
7. **Performance trade-offs:** Design choices that improve one workload may make another less efficient.

---

## 21. Common Misconceptions

### Misconception 1: A file system is the same as a folder.

**Correction:** A directory is used to organize files. A file system is the broader mechanism that manages files, directories, metadata, allocation, and storage-related structures.

### Misconception 2: The file name tells the computer exactly what the content is.

**Correction:** Extensions are useful conventions, but they do not guarantee that the contents match the extension.

### Misconception 3: Deleting a file always erases its contents immediately.

**Correction:** Deletion commonly removes a reference and allows storage to be reused. Physical erasure is a separate issue.

### Misconception 4: Every file is stored in consecutive blocks.

**Correction:** Files can use contiguous or non-contiguous allocation. Many real-world systems support multiple allocation patterns.

### Misconception 5: A partition and a file system are the same thing.

**Correction:** A partition defines a storage region. A file system organizes data within a suitable storage region or volume.

### Misconception 6: Journaling guarantees that no data will ever be lost.

**Correction:** Journaling helps with recovery and consistency, but it does not eliminate every kind of data loss.

### Misconception 7: Every file system uses the same internal structure.

**Correction:** File systems have different designs, metadata formats, allocation techniques, and reliability features.

### Misconception 8: Free-space management and file allocation are identical.

**Correction:** File allocation records where file data belongs. Free-space management tracks which storage units remain available.

---

## 22. Real-World Examples

### Example 1: Saving college notes

You save `OS_notes.txt` inside `College`.

The file system organizes its name and metadata, tracks its data blocks, and makes it accessible through the appropriate directory path.

**Concepts involved:** Files, directories, metadata, allocation.

### Example 2: Downloading a video

A browser downloads `lecture.mp4`.

The operating system manages the file operations, and the file system allocates and tracks the storage needed for the downloaded data.

**Concepts involved:** File creation, writing, allocation, free-space management.

### Example 3: Connecting a USB drive

You connect a FAT32-formatted USB drive to a supported computer.

The operating system recognizes the device and file system, then makes the files accessible if mounting succeeds.

**Concepts involved:** File-system types, partitions, mounting, compatibility.

### Example 4: Sharing a computer

Two users have different home directories.

Each user can store personal documents separately, and permissions can control access to those documents.

**Concepts involved:** Directory structures, naming, permissions.

### Example 5: Sudden power failure

The computer loses power during a metadata update.

A journaling file system may use its journal to recover a consistent file-system state during recovery.

**Concepts involved:** Journaling, consistency, recovery.

### Example 6: Running out of disk space

You try to download a large file, but the storage volume has insufficient available space.

The file system cannot allocate the required space unless enough suitable capacity becomes available or the operation can proceed with less space.

**Concepts involved:** Free-space management, allocation, capacity.

---

## 23. Interview and Exam Questions

### Q1. What is a file system?

A file system is a method and set of structures used by an operating system to organize, name, store, retrieve, and manage files on a storage device.

### Q2. Why do we need a file system?

It allows the operating system to manage file names, directories, metadata, storage allocation, free space, and access permissions efficiently.

### Q3. Name four popular file systems.

FAT32, NTFS, ext4, and APFS.

### Q4. What is a directory?

A directory is a structure that organizes names and references to files and usually other directories.

### Q5. What is metadata?

Metadata is information describing a file or file-system object, such as its size, permissions, timestamps, and storage-related information.

### Q6. What are the three classic file allocation methods?

Contiguous allocation, linked allocation, and indexed allocation.

### Q7. Which allocation method requires consecutive blocks?

Contiguous allocation.

### Q8. Which allocation method uses links between data blocks?

Linked allocation.

### Q9. Which allocation method uses an index containing block references?

Indexed allocation.

### Q10. What is external fragmentation?

External fragmentation occurs when free storage is divided into separate regions, making it difficult to allocate a sufficiently large contiguous region.

### Q11. What is free-space management?

It is the process of tracking which storage blocks or allocation units are available for use.

### Q12. What is a bitmap?

A bitmap represents the allocation state of storage units using bits.

### Q13. What is a free list?

A free list maintains references to storage blocks that are available for allocation.

### Q14. What is the difference between an absolute and a relative path?

An absolute path starts from a defined root, while a relative path is interpreted from a specified starting location.

### Q15. What is a VFS?

A Virtual File System provides a common interface for supported file-system implementations within an operating system.

### Q16. What is mounting?

Mounting makes a file system accessible through an operating-system namespace, commonly at a directory or volume entry point.

### Q17. What is journaling?

Journaling records selected intended file-system changes in a log to support recovery and consistency after interrupted operations.

### Q18. Does deleting a file immediately erase all its data?

No. Deletion commonly removes the file's reference and permits storage reuse. Immediate physical erasure is not guaranteed.

### Q19. Why is NTFS useful?

NTFS supports features such as access-control permissions, journaling, large files, and other Windows-oriented file-management capabilities.

### Q20. Why is FAT32 still useful?

It offers broad compatibility with many devices and operating systems, although its individual-file size limit and other limitations must be considered.

### Q21. What is the difference between a partition and a file system?

A partition defines a region of storage, while a file system defines how data is organized and managed within a suitable storage region or volume.

### Q22. Does every file system support the same permissions and recovery features?

No. Capabilities depend on the file-system design, operating system, configuration, and implementation.

---

## 24. Practice Exercises

Try solving these without looking at the answers immediately.

### Exercise 1: Identify the concept

You save `maths.pdf` inside a folder named `College`.

Identify:

1. The file.
2. The directory.
3. The extension.
4. One example of metadata.

### Exercise 2: Choose an allocation method

A file system needs to store a file using blocks 4, 9, 12, and 18.

Which classic allocation method can represent these scattered blocks using links between them?

### Exercise 3: Understand a bitmap

A file system uses `0` for free blocks and `1` for allocated blocks.

```text
Bitmap: 1 0 1 0 0 1 0 1
```

Answer:

1. Which blocks are free?
2. Which blocks are allocated?
3. How many free blocks exist?

### Exercise 4: Compare directories

Which directory structure naturally supports nested groups such as:

```text
College/
    OS/
    Maths/
    Physics/
```

### Exercise 5: Explain deletion

A user deletes a file, but its previous contents might still exist on the storage device.

Explain why this is possible.

### Exercise 6: Calculate bitmap size

A system tracks 80,000 allocation units with one bit per unit.

How many bytes are required for the bitmap, ignoring extra overhead?

### Exercise 7: Choose a file system

Which would normally be a reasonable choice for:

1. A modern Windows system volume.
2. A common Linux installation.
3. A modern Apple storage volume.
4. A removable drive that needs broad compatibility and stores no individual file larger than the FAT32 limit.

### Exercise 8: Explain the complete process

Describe what happens when a user saves a new file from a text editor.

Include the application, operating system, directory, metadata, allocation, and storage operations.

### Exercise 9: Correct the statement

"Free-space management tells the operating system which blocks belong to a particular file."

Is this correct? If not, correct it.

### Exercise 10: Teach someone else

Explain the difference between contiguous, linked, and indexed allocation using a real-life analogy.

### Answer key

**1.** File: `maths.pdf`. Directory: `College`. Extension: `.pdf`. Metadata: size, permissions, or modification time.

**2.** Linked allocation can represent the scattered blocks by linking them in the required order. Indexed allocation can also reference scattered blocks, but through an index.

**3.** Free blocks: 1, 3, 4, and 6. Allocated blocks: 0, 2, 5, and 7. Total free blocks: 4. Block numbering begins at 0.

**4.** A tree-structured directory.

**5.** Deletion commonly removes a directory reference or equivalent file-system entry and makes the storage available for reuse. The old contents may remain until overwritten or otherwise erased.

**6.** \(80{,}000 \div 8 = 10{,}000\) bytes.

**7.** NTFS; ext4; APFS; FAT32, respectively, assuming the relevant operating system and device support the choice.

**8.** The application requests a save, the operating system resolves the path and checks access rules, the file system establishes or updates metadata, storage is allocated as needed, and the data and relevant metadata are written through the storage stack.

**9.** Incorrect. Free-space management tracks available blocks. The file-allocation structures track the blocks belonging to a file.

**10.**
- Contiguous: seats reserved together in one consecutive row.
- Linked: a treasure hunt where each clue points to the next.
- Indexed: a textbook index listing the locations of all relevant pages.

---

## 25. Revision Cheat Sheet

| Term | Meaning in one line |
|---|---|
| File system | Organizes and manages files and storage |
| File | A named collection of information |
| Metadata | Information describing a file or file-system object |
| Directory | Organizes names and references to file-system objects |
| VFS | Common interface for different file-system implementations |
| Partition | A defined region of a storage device |
| Mounting | Making a file system accessible in an operating system |
| Contiguous allocation | A file uses consecutive blocks |
| Linked allocation | A file's blocks are connected by links |
| Indexed allocation | An index identifies a file's data blocks |
| Bitmap | Tracks allocation states using bits |
| Free list | Maintains references to free blocks |
| Grouping | Stores several free-block references together |
| Counting | Represents a consecutive run using a start and length |
| External fragmentation | Free space is divided into separate regions |
| Journaling | Records selected changes to aid recovery |
| Absolute path | Path from a defined root |
| Relative path | Path interpreted from a starting location |
| FAT32 | Widely compatible file system with a 4 GiB-minus-1-byte file-size limit |
| NTFS | Windows-oriented file system with rich management features |
| ext4 | Widely used Linux file system |
| APFS | Modern Apple file system |

### Five facts to remember

1. A file system organizes data and tracks where it is stored.
2. Directories organize names and references to files.
3. Contiguous, linked, and indexed are the three classic file allocation methods.
4. Bitmaps and free lists help manage available storage.
5. File systems differ in compatibility, permissions, allocation, and reliability features.

---

## 26. Explain File Systems in 60 Seconds

Use this as your first speaking exercise. Once you understand the ideas, explain them naturally rather than memorizing every sentence.

"A file system is the part of the operating system that organizes and manages files on storage devices such as SSDs and hard drives.

Think of it like a library. Files are books, directories are shelves, and metadata is the information that describes each book. The file system remembers where files are stored and helps the operating system read, write, and manage them.

There are different file systems, including FAT32, NTFS, ext4, and APFS. They have different features and compatibility.

Three classic file allocation methods are contiguous, linked, and indexed allocation. Contiguous allocation uses consecutive blocks, linked allocation connects blocks through links, and indexed allocation uses an index to locate blocks.

The file system also manages free space using techniques such as bitmaps and free lists. Additional mechanisms, including permissions and journaling, can help protect data and maintain consistency.

In short, a file system makes storage organized, accessible, and manageable."

---

## 27. Final Self-Test

Before moving to the next operating-system topic, make sure you can answer these questions without reading the notes.

- [ ] Can I define a file system in my own words?
- [ ] Can I explain the difference between a file, a directory, and metadata?
- [ ] Can I describe the basic path from an application to a storage device?
- [ ] Can I compare FAT32, NTFS, ext4, and APFS?
- [ ] Can I explain single-level, two-level, and tree-structured directories?
- [ ] Can I distinguish absolute paths from relative paths?
- [ ] Can I draw contiguous, linked, and indexed allocation?
- [ ] Can I explain external fragmentation?
- [ ] Can I calculate the size of a bitmap?
- [ ] Can I distinguish file allocation from free-space management?
- [ ] Can I explain mounting, permissions, and journaling?
- [ ] Can I describe what happens when a file is saved or deleted?
- [ ] Can I answer the interview questions without looking at the answers?
- [ ] Can I explain the entire topic in two minutes using my own examples?

### Your learning strategy

**Round 1 — Understand:** Read each section and connect it to the everyday analogies.

**Round 2 — Draw:** Draw the directory tree, the three allocation methods, and a bitmap without looking at the notes.

**Round 3 — Recall:** Close the document and explain each concept aloud.

**Round 4 — Teach:** Pretend you are explaining file systems to a friend who has never studied operating systems.

**Round 5 — Test:** Complete the practice exercises and revisit only the concepts you could not explain.

You do not need to memorize every sentence. Your goal is to understand what each component does, why it exists, and how it connects to the other components.

---

## Conclusion

A file system is much more than a collection of folders. It is a structured mechanism that enables an operating system to manage names, directories, metadata, storage allocation, free space, and file access.

The easiest way to master this topic is to connect every technical concept to one central question:

**How does the operating system organize data, remember where it is, and retrieve it safely and efficiently?**

Once you can answer that question, the details of file allocation, directory structures, free-space management, and file-system types become much easier to understand.

**Next topic to study:** Disk Management in Operating Systems, followed by I/O Systems and Protection and Security.
