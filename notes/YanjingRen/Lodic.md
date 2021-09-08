# Lodic: Logical Distributed Counting for Scalable File Access

Authors: Jeoungahn Park, KAIST; Taeho Hwang, Hanyang University; Jongmoo Choi, Dankook University; Changwoo Min, Virginia Tech; Youjip Won, KAIST
Created: August 26, 2021 7:08 PM
Paper Link: https://www.usenix.org/conference/atc21/presentation/park
Paper PDF: https://www.usenix.org/system/files/atc21-park.pdf
Published: USENIX ATC'21
Tags: Storage, System

# Background

1. Reference Counts. The idea of using a reference counter to manage the lifetime of an object is fairly common. The core idea is to have a counter which is incremented whenever a new reference is taken and decremented when a reference is released.
2. Per-core distributed reference count: This approach manages the per-core local counters in addition to the central object counter. In the common case, each thread accesses its per-core local counter, not incurring cacheline bouncing.
3. Hash table based reference count: They store local counters at the per-core hash table to bound the memory usage regardless of the number of objects.

# The problem

Existing per-core based distributed counting schemes faces excessive memory pressure and the counter query latency issues.

# Key Findings

- Reference counter is widely used in Linux kernel for resource management. Use cacheline management as an example, authors found that most cacheline contention in updating the reference counter is driven by the contention among the processes rather than processors.
- Millions of counters are not uncommon

- Not all file pages are popular

- Reference duration is extremely short in File Access

# Main idea

**Allocate the local counters for a given object to each process that accesses it.**

# Design

- File mapping: map file block to process address space

    Save memory (the number of calculations is related to the number of processes, and the number of CPU cores), and improve retrieval efficiency (number of inspection results)

- Reverse mapping: between process address space and file’s address space

    Determines the upper limit of performance. 

    Quickly find the VMA of page mapped shared file block

- Counter embedding: use unused bits in page table entry

    Avoid reallocating memory space and reduce the search overhead of additional indexes