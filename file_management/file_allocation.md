 
# File Allocation Methods in Operating System

## Table of Contents

1. [Introduction to File Allocation Methods](#1-introduction-to-file-allocation-methods)
2. [Understanding Disk Blocks](#2-understanding-disk-blocks)
3. [Why Do We Need File Allocation Methods?](#3-why-do-we-need-file-allocation-methods)
4. [Types of File Allocation Methods](#4-types-of-file-allocation-methods)
5. [Contiguous Allocation](#5-contiguous-allocation)
6. [Linked Allocation](#6-linked-allocation)
7. [Indexed Allocation](#7-indexed-allocation)
8. [Indexing Schemes for Large Files](#8-indexing-schemes-for-large-files)
9. [Understanding Inode-Based Allocation](#9-understanding-inode-based-allocation)
10. [Comparison of All Allocation Methods](#10-comparison-of-allocation-methods)
11. [Real-World Examples](#11-real-world-examples)
12. [Interview Questions and Answers](#12-interview-questions-and-answers)
13. [Practical Exercises](#13-practical-exercises)
14. [Revision Cheat Sheet](#14-revision-cheat-sheet)
15. [Explain File Allocation Methods in 60 Seconds](#15-explain-file-allocation-methods-in-60-seconds)
16. [Final Self-Assessment](#16-final-self-assessment)

---

## 1. Introduction to File Allocation Methods

Before understanding file allocation methods, let's understand a simple problem.

Imagine you have a notebook containing 100 pages.

You want to store three different subjects:

- Operating Systems
- Computer Networks
- Database Management Systems

How will you arrange the pages?

You could:

1. Keep all pages of each subject together.
2. Store pages in different locations and connect them using references.
3. Maintain an index that tells you where every page is located.

An operating system faces a similar problem when storing files on a disk.

A disk is divided into smaller storage units called **blocks**.

When a file is saved, the operating system must decide which disk blocks should store that file.

The method used to assign those blocks is called a **file allocation method**.

### Definition

File allocation methods are techniques used by an operating system to allocate disk blocks to files.

### Main Objectives

File allocation methods aim to provide:

- Efficient disk-space utilization.
- Fast file access.
- Support for file growth.
- Minimum storage overhead.
- Reliable file management.

## 2. Understanding Disk Blocks

Before learning the three allocation methods, understand disk blocks.

A storage device is divided into fixed-size units called blocks.

For example, imagine a disk containing 20 blocks:

```text
+------+------+------+------+------+
|  0   |  1   |  2   |  3   |  4   |
+------+------+------+------+------+
|  5   |  6   |  7   |  8   |  9   |
+------+------+------+------+------+
|  10  |  11  |  12  |  13  |  14  |
+------+------+------+------+------+
|  15  |  16  |  17  |  18  |  19  |
+------+------+------+------+------+
```

Each numbered box represents one disk block.

Suppose a file requires 4 blocks.

The operating system must allocate 4 blocks to that file.

The three allocation methods differ in how these blocks are selected and connected.

### Important Terms

| Term | Meaning |
|---|---|
| Disk block | Fixed-size storage unit |
| Allocation | Assigning disk blocks to a file |
| Fragmentation | Wasted or inefficiently used storage space |
| Pointer | Address or reference to another block |
| Index block | A block containing addresses of file data blocks |
| Data block | A block that stores actual file contents |

## 3. Why Do We Need File Allocation Methods?

Suppose a user saves a file called `notes.txt`.

The file requires 4 disk blocks.

The operating system needs to answer:

- Where should the first block be stored?
- Where should the remaining blocks be stored?
- How can the next block be found?
- How can the file be accessed quickly?
- What happens if the file grows?

Different allocation methods answer these questions differently.

For example:

**Contiguous Allocation:** Store all 4 blocks together.

**Linked Allocation:** Store blocks anywhere and connect them using pointers.

**Indexed Allocation:** Store block addresses in a separate index block.

## 4. Types of File Allocation Methods

There are three main traditional file allocation methods.

```text
             File Allocation Methods
                       |
          ---------------------------
          |            |            |
      Contiguous     Linked       Indexed
      Allocation   Allocation   Allocation
```

Let's understand each one in detail.

## 5. Contiguous Allocation

### What Is Contiguous Allocation?

In contiguous allocation, all blocks belonging to a file are stored next to each other on the disk.

The word **contiguous** means continuous or adjacent.

Think of reserving consecutive seats in a cinema.

If four people want to sit together, they need four neighboring seats.

Similarly, a file gets consecutive disk blocks.

### Diagram

Suppose a file requires 4 blocks.

The operating system allocates blocks 4, 5, 6, and 7.

```text
Disk Blocks:

+----+----+----+----+----+----+----+----+----+
| 0  | 1  | 2  | 3  | 4  | 5  | 6  | 7  | 8  |
+----+----+----+----+----+----+----+----+----+
                    |    |    |    |
                    +----+----+----+
                       FILE A
```

The file occupies consecutive blocks.

### How Does It Work?

The directory entry stores two important pieces of information:

1. Starting block address.
2. Length of the allocated portion, usually expressed as the number of blocks.

For example:

```text
File Name: notes.txt
Starting Block: 4
Length: 4 blocks
```

The file occupies:

```text
4, 5, 6, 7
```

### Formula

If:

- `b` = starting block.
- `k` = file block number, starting from 0.

Then:

\[
\text{Block Address} = b + k
\]

Example:

Starting block = 4.

Find the fourth logical block (using zero-based numbering):

\[
4 + 3 = 7
\]

Therefore, the fourth logical block is stored at disk block 7.

### Advantages

**1. Fast Sequential Access**

The blocks are stored together, so reading them sequentially is efficient.

**2. Fast Direct Access**

The operating system can calculate the address of any block directly.

**3. Less Seeking**

On traditional hard disks, neighboring blocks can often be read with less mechanical head movement.

**4. Simple Implementation**

Only the starting block and allocated length need to be stored in the directory entry.

### Disadvantages

**1. External Fragmentation**

Free space may become scattered into small gaps.

Even if the total free space is sufficient, there may not be one large consecutive region.

Example:

```text
Disk:

[Free][A][A][Free][B][B][Free][C][C][Free]
```

Suppose a new file needs 4 consecutive blocks.

There are 4 free blocks in total, but they are scattered.

The file cannot be allocated in this arrangement.

This is called external fragmentation.

**2. File Growth Is Difficult**

Suppose a file occupies blocks 4, 5, 6, and 7.

It grows and now requires 6 blocks.

If blocks 8 and 9 are already occupied, the file cannot simply extend in place.

The operating system may need to relocate the file to a larger contiguous region.

**3. File Size May Need to Be Estimated**

The system needs to know how much contiguous space to allocate when creating or extending a file.

### When Is It Useful?

- When fast sequential and direct access are important.
- When file sizes are known in advance.
- In storage arrangements where contiguous extents can be reserved efficiently.

**Remember:** Contiguous allocation = All file blocks are stored together.

## 6. Linked Allocation

### What Is Linked Allocation?

In linked allocation, file blocks can be stored anywhere on the disk.

Each block contains a pointer to the next block belonging to the same file.

Think of a treasure hunt.

You find the first clue, which tells you where to find the second clue. The second tells you where to find the third, and so on.

Similarly, each file block points to the next file block.

### Diagram

Suppose a file occupies blocks:

```text
4 → 9 → 2 → 15
```

The blocks are not adjacent.

```text
Block 4
+----------------+
| File Data      |
| Next: 9        |
+----------------+
        |
        v
Block 9
+----------------+
| File Data      |
| Next: 2        |
+----------------+
        |
        v
Block 2
+----------------+
| File Data      |
| Next: 15       |
+----------------+
        |
        v
Block 15
+----------------+
| File Data      |
| Next: NULL     |
+----------------+
```

The last block contains a null pointer indicating that there is no next block.

### How Does It Work?

The directory entry stores:

1. Address of the first block.
2. Address of the last block (in the described implementation).

Each block stores:

- Actual file data.
- A pointer to the next block.

Example:

```text
File Name: jeep
Starting Block: 4
Ending Block: 15
```

Block chain:

```text
4 → 9 → 2 → 15 → NULL
```

### How Do We Read a File?

Suppose we want to read the fourth block.

The operating system must follow the pointers:

1. Start at block 4.
2. Follow the pointer to block 9.
3. Follow the pointer to block 2.
4. Follow the pointer to block 15.

This is why traditional linked allocation is good for sequential access but inefficient for direct access.

### Advantages

**1. No External Fragmentation**

Blocks do not need to be consecutive.

The operating system can allocate any suitable free block.

**2. Easy File Growth**

When a file grows, the operating system can allocate another free block and connect it to the existing chain.

**3. Flexible Allocation**

File blocks can be distributed across the disk.

### Disadvantages

**1. No Efficient Direct Access**

To reach a particular block, the system must follow the pointers from the beginning.

**2. Pointer Overhead**

Every block must reserve some space for the pointer.

This reduces the space available for file data.

**3. Slower Access on Hard Disks**

Blocks may be scattered across the disk, causing additional seek operations.

**4. Pointer Corruption**

If a pointer is damaged, the remaining part of the file chain may become inaccessible.

### When Is It Useful?

- Files that grow dynamically.
- Sequential access workloads.
- Systems where avoiding external fragmentation is important.

### Important Variation: FAT

The File Allocation Table (FAT) is a variation of linked allocation.

Instead of storing the next-block pointer inside every data block, it stores block-chain information in a separate table.

Example:

```text
FAT Table:

Block 4  → 9
Block 9  → 2
Block 2  → 15
Block 15 → EOF
```

This allows the data blocks to contain file data without reserving a pointer field inside each block.

**Remember:** Linked allocation = Blocks can be anywhere + Each block points to the next block.

## 7. Indexed Allocation

### What Is Indexed Allocation?

In indexed allocation, each file has a separate block called an **index block**.

The index block stores the addresses of all data blocks belonging to that file.

Think of a book's index.

Instead of searching every page to find a topic, you look at the index to find its page number.

Similarly, the operating system looks at the index block to find the disk block containing the required file data.

### Diagram

Suppose a file occupies blocks:

```text
4, 9, 2, 15
```

The index block contains all these addresses.

```text
             Index Block
          +----------------+
          | 4              |
          | 9              |
          | 2              |
          | 15             |
          +----------------+
             |   |   |   |
             v   v   v   v
          +---+---+---+----+
          | 4 | 9 | 2 | 15 |
          +---+---+---+----+
             Data Blocks
```

The data blocks do not need to be adjacent.

### How Does It Work?

1. The operating system creates an index block for the file.
2. The index block stores the addresses of the file's data blocks.
3. The directory entry stores the address of the index block.
4. To access a particular file block, the operating system checks the corresponding index entry.
5. It then accesses the required data block.

### Example

Suppose the index block contains:

| Index Entry | Disk Block |
|---|---:|
| 0 | 4 |
| 1 | 9 |
| 2 | 2 |
| 3 | 15 |

If the user requests logical block 2 (zero-based numbering), the operating system checks index entry 2.

It finds disk block 2.

Therefore, the requested data is read from disk block 2.

### Advantages

**1. Direct Access**

The system can locate a file block using its index entry without traversing all previous data blocks.

**2. No External Fragmentation**

Data blocks can be allocated anywhere on the disk.

**3. Easy File Growth**

Additional block addresses can be added to the index structure, subject to index capacity.

**4. Flexible Allocation**

The file's data blocks do not need to be stored together.

### Disadvantages

**1. Index Block Overhead**

Each file needs index storage in addition to its data blocks.

**2. Inefficient for Very Small Files**

A small file may need only one or two data blocks, but a separate index block can consume substantial space.

**3. Index Size Limitation**

A single index block can store only a limited number of block addresses.

**4. Additional Access**

The system may need to read the index block before accessing the data block, unless the index information is already cached.

### When Is It Useful?

- When direct access is important.
- When files may have non-contiguous data blocks.
- When a file system needs flexible block mapping.

**Remember:** Indexed allocation = One index block stores the addresses of the file's data blocks.

## 8. Indexing Schemes for Large Files

### Why Do We Need Advanced Indexing?

Suppose an index block is 4 KB.

Each block address requires 4 bytes.

The number of addresses that fit in one index block is:

\[
\frac{4096}{4}=1024
\]

Therefore, one index block can store 1,024 block addresses.

But what happens if a file needs 10,000 data blocks?

A single index block is not enough.

To solve this problem, larger indexing schemes are used.

There are three important approaches:

1. Linked Scheme.
2. Multilevel Index.
3. Combined Scheme (Inode Structure).

### 8.1 Linked Scheme

In the linked scheme, multiple index blocks are connected together.

Each index block contains:

- Some data-block addresses.
- A pointer to the next index block.

Diagram:

```text
Index Block 1
+-------------------+
| Data Block: 4     |
| Data Block: 9     |
| Next Index: 20    |
+-------------------+
          |
          v
Index Block 20
+-------------------+
| Data Block: 12    |
| Data Block: 18    |
| Next Index: 35    |
+-------------------+
          |
          v
Index Block 35
+-------------------+
| Data Block: 22    |
| Data Block: 30    |
| Next Index: NULL  |
+-------------------+
```

The system follows the index-block chain to find all data blocks.

**Advantages:**

- Supports files larger than one index block can describe.
- Can extend by adding another index block.

**Disadvantages:**

- Additional pointer overhead.
- Finding a later index block may require traversing earlier index blocks.

### 8.2 Multilevel Index

In multilevel indexing, index blocks are arranged in multiple levels.

The first-level index block points to second-level index blocks.

Those second-level index blocks point to actual data blocks.

Diagram:

```text
             Level 1
          Index Block
               |
        ----------------
        |              |
        v              v
     Level 2        Level 2
    Index Block    Index Block
        |              |
      ------         ------
      |    |         |    |
      v    v         v    v
     Data Data      Data Data
    Blocks Blocks  Blocks Blocks
```

This structure can be extended to three or more levels.

**Advantages:**

- Supports very large files.
- Provides a structured way to manage many block addresses.
- Can scale by adding more levels.

**Disadvantages:**

- Additional levels can require additional disk accesses.
- More complicated to manage than a single index block.

### 8.3 Combined Scheme (Inode Structure)

A combined indexing scheme uses several kinds of pointers in one inode.

It is designed to provide efficient access for both small and large files.

The inode may contain:

1. Direct pointers.
2. Single indirect pointer.
3. Double indirect pointer.
4. Triple indirect pointer.

We will study this in detail in the next section.

## 9. Understanding Inode-Based Allocation

### What Is an Inode?

An inode (index node) is a data structure used in Unix-like file systems to store information about a file.

It commonly stores:

- File type.
- File permissions.
- File owner information.
- File size.
- Timestamps.
- Pointers or mappings to file data blocks.

A filename is normally stored in a directory entry, which maps the filename to an inode number.

### Why Does an Inode Need Different Pointers?

A small file does not need a complicated indexing structure.

A large file needs many block addresses.

Using different pointer levels allows the file system to support both.

### 1. Direct Pointers

A direct pointer points directly to a data block.

Example:

```text
Inode
  |
  +----> Data Block 1
  |
  +----> Data Block 2
  |
  +----> Data Block 3
```

Direct pointers are useful for small files because the system can reach the data blocks without following an indirect index block.

### 2. Single Indirect Pointer

A single indirect pointer points to an indirect block.

That indirect block contains addresses of data blocks.

```text
Inode
  |
  v
Indirect Block
  |
  +----> Data Block 1
  |
  +----> Data Block 2
  |
  +----> Data Block 3
```

### 3. Double Indirect Pointer

A double indirect pointer points to a block containing addresses of single-indirect blocks.

Those indirect blocks point to the actual data blocks.

```text
Inode
  |
  v
Double Indirect Block
  |
  +----> Indirect Block 1
  |            |
  |            +----> Data Blocks
  |
  +----> Indirect Block 2
               |
               +----> Data Blocks
```

### 4. Triple Indirect Pointer

A triple indirect pointer adds another level of indirection.

```text
Inode
  |
  v
Triple Indirect Block
  |
  v
Double Indirect Blocks
  |
  v
Single Indirect Blocks
  |
  v
Data Blocks
```

### Complete Inode Structure

```text
                 INODE
                   |
       --------------------------
       |      |       |         |
       v      v       v         v
     Direct Single  Double    Triple
   Pointers Indirect Indirect Indirect
              |       |         |
              v       v         v
           Data    Indirect  Double
           Blocks   Blocks   Indirect
                              Blocks
                                |
                                v
                           Single Indirect
                              Blocks
                                |
                                v
                            Data Blocks
```

### Why Is the Combined Scheme Preferred?

**1. Efficient for Small Files**

Direct pointers provide access without extra indexing levels.

**2. Supports Large Files**

Indirect pointers allow the file system to address many more data blocks.

**3. Flexible**

Different pointer levels support different file sizes.

**4. Better Scalability**

The system can handle files that exceed the capacity of direct pointers.

### Where Is It Used?

Traditional inode-based block mapping is associated with Unix file systems such as ext2 and ext3.

Important accuracy note: ext4 supports extents as its modern file-block mapping mechanism in many cases, rather than relying exclusively on the traditional direct/single/double/triple indirect pointer scheme.

## 10. Comparison of All Allocation Methods

| Feature | Contiguous | Linked | Indexed |
|---|---|---|---|
| Block arrangement | Consecutive | Scattered | Scattered |
| Direct access | Yes | No, not efficiently | Yes |
| Sequential access | Yes | Yes | Yes |
| External fragmentation | Yes | No | No |
| File growth | Difficult | Easy | Easy, within index capacity |
| Pointer overhead | Low | Per data block | Index block |
| Small-file efficiency | Depends on allocation | Pointer overhead | Index overhead |
| Main limitation | External fragmentation | Slow direct access | Index storage overhead |

### Easy Comparison

| Method | Main Idea |
|---|---|
| Contiguous | Store blocks together |
| Linked | Connect blocks using pointers |
| Indexed | Store block addresses in an index |

## 11. Real-World Examples

### Example 1: Contiguous Allocation

Imagine reserving four consecutive seats for your friends.

```text
Seats:  1  2  3  4  5  6  7  8
        X  X  X  X
```

All seats are together.

Similarly, a file gets consecutive disk blocks.

### Example 2: Linked Allocation

Imagine a treasure hunt where each clue tells you where to find the next clue.

```text
Clue 1 → Clue 5 → Clue 3 → Clue 8
```

Similarly, each disk block points to the next block.

### Example 3: Indexed Allocation

Imagine a book's index that tells you which page contains a particular topic.

```text
Index:
Topic A → Page 10
Topic B → Page 25
Topic C → Page 40
```

Similarly, an index block stores the addresses of file data blocks.

## 12. Interview Questions and Answers

### Q1. What is file allocation?

File allocation is the process of assigning disk blocks to files.

### Q2. What are the three main file allocation methods?

1. Contiguous allocation.
2. Linked allocation.
3. Indexed allocation.

### Q3. Which allocation method stores blocks consecutively?

Contiguous allocation.

### Q4. Which allocation method uses pointers in data blocks?

Linked allocation.

### Q5. Which allocation method uses an index block?

Indexed allocation.

### Q6. What is external fragmentation?

External fragmentation occurs when free disk space is divided into small non-contiguous regions.

A file may be unable to obtain a sufficiently large consecutive region even when total free space is sufficient.

### Q7. Which allocation method suffers from external fragmentation?

Contiguous allocation.

### Q8. Which allocation methods avoid external fragmentation?

Linked allocation and indexed allocation.

### Q9. Which allocation method supports direct access?

Contiguous and indexed allocation.

### Q10. Why is linked allocation inefficient for direct access?

Because the system must follow pointers from the beginning of the file to reach the required block.

### Q11. What is the main disadvantage of indexed allocation?

It requires additional storage for index blocks.

### Q12. What is FAT?

FAT stands for File Allocation Table.

It is a variation of linked allocation in which block-chain information is maintained in a separate table.

### Q13. What is an inode?

An inode is a file-system data structure that stores file metadata and information needed to locate the file's data.

### Q14. What is a direct pointer?

A direct pointer points directly to a data block.

### Q15. What is a single indirect pointer?

It points to an index block containing addresses of data blocks.

### Q16. What is a double indirect pointer?

It points to a block containing addresses of single-indirect blocks.

### Q17. What is a triple indirect pointer?

It adds another level of indirection to support very large files.

### Q18. Why is inode-based combined indexing useful?

It provides efficient access for small files through direct pointers and supports large files through indirect pointers.

## 13. Practical Exercises

### Exercise 1: Contiguous Allocation

A file starts at disk block 10 and requires 5 blocks.

Find all allocated blocks.

### Exercise 2: Linked Allocation

A file has the following block chain:

```text
5 → 12 → 8 → 20 → NULL
```

Answer:

1. What is the starting block?
2. What is the ending block?
3. Which block points to block 20?
4. Can block 20 be accessed directly without following the chain?

### Exercise 3: Indexed Allocation

An index block contains:

| Index | Block Address |
|---|---:|
| 0 | 10 |
| 1 | 15 |
| 2 | 20 |
| 3 | 25 |

Which disk block contains logical block 2?

### Exercise 4: Calculate Index Capacity

An index block has a size of 2,048 bytes.

Each block address requires 4 bytes.

How many addresses can it store?

### Exercise 5: Identify the Allocation Method

Identify the allocation method:

1. All blocks are stored next to each other.
2. Each block contains a pointer to the next block.
3. A separate block stores all data-block addresses.
4. A file can grow by adding another block and linking it to the previous one.

### Exercise 6: Conceptual Questions

1. Why does contiguous allocation suffer from external fragmentation?
2. Why does linked allocation not support efficient direct access?
3. Why does indexed allocation require additional storage?
4. Why are direct pointers useful for small files?
5. Why do large files need multilevel indexing?

## 14. Revision Cheat Sheet

| Concept | Important Point |
|---|---|
| File allocation | Assigning disk blocks to files |
| Contiguous allocation | Blocks are consecutive |
| Linked allocation | Blocks are connected using pointers |
| Indexed allocation | Index block stores block addresses |
| External fragmentation | Free space is scattered |
| Direct access | Access a block without traversing earlier blocks |
| FAT | Separate table stores linked block information |
| Inode | Stores metadata and data-block mapping information |
| Direct pointer | Points to data block |
| Single indirect | Points to a block of data-block addresses |
| Double indirect | Points to blocks of indirect-block addresses |
| Triple indirect | Adds another indexing level |

### Three Methods in One Line

1. Contiguous: Blocks together.
2. Linked: Blocks connected.
3. Indexed: Addresses collected in an index block.

## 15. Explain File Allocation Methods in 60 Seconds

File allocation methods describe how an operating system assigns disk blocks to files.

There are three main methods.

First, **contiguous allocation** stores all file blocks next to each other. It provides fast sequential and direct access, but suffers from external fragmentation and makes file growth difficult.

Second, **linked allocation** stores blocks anywhere on the disk and connects them using pointers. It avoids external fragmentation and supports easy file growth, but direct access is inefficient.

Third, **indexed allocation** uses a separate index block containing addresses of all the file's data blocks. It supports direct access and avoids external fragmentation, but requires extra index storage.

For large files, linked index blocks, multilevel indexing, and inode-based structures can be used.

## 16. Final Self-Assessment

Test your understanding.

1. What is file allocation?
2. What are the three main allocation methods?
3. Which method stores blocks consecutively?
4. Which method uses pointers to connect blocks?
5. Which method uses an index block?
6. What is external fragmentation?
7. Which allocation method suffers from external fragmentation?
8. Why is linked allocation inefficient for direct access?
9. What is the purpose of an index block?
10. What is FAT?
11. What is an inode?
12. What is the difference between direct and indirect pointers?
13. Why are multilevel indexes needed?
14. What is the advantage of the combined inode scheme?
15. Compare contiguous, linked, and indexed allocation.

### Final Learning Goal

After studying this topic, you should be able to:

- Explain all three file allocation methods.
- Draw their disk-block diagrams.
- Calculate block addresses in contiguous allocation.
- Trace pointers in linked allocation.
- Find file blocks using an index block.
- Explain external fragmentation.
- Understand FAT and inode-based indexing.
- Answer common OS interview questions.
