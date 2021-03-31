**Title:** **Primary Data Deduplication – Large Scale Study and System Design**

**Source:** **ATC;12**

**Authors:** Ahmed El-Shimi, Ran Kalach, Ankit Kumar, Adi Oltean, Jin Li, Sudipta Sengupta 

------

**Summary**

- The problem the paper aims to solve

  Primary dataset different from backup dataset, has some special characteristics, which needs some different ideas and techniques to perform deduplication meanwhile balancing resource consumption, space saving, and throughput . 

- How can the paper address the problem? What is the main idea of this paper?

  - **Regression chunking algorithm**

    - **Basic idea: ** relax match condition to some suffix of bit pattern P
    - **Method: ** match |P| - i bits of P, with decreasing priority for i = 0, 1, …, k (k is an adjustable parameter)   

  - **New chunking index**

    - **Log-structured organization: **Chunk metadata organized in log-structure on disk; Insertions aggregated in write buffer in RAM and appended to log in single I/O
    - **Low RAM footprint index: **A specialized in-memory hash table is used to index chunk metadata records on secondary storage; 2-byte signature, 4-byte pointer per entry => 6-byte of RAM per indexed chunk
    - **Prefetch cache: ** Prefetch chunk mappings for next 1000 chunks in a single I/O; Prefetch cache size at 100000 entries (5MB of RAM)

  - **Partition and Reconciliation**

    - **Partition by file type**
    - **Dedup in each partition rather than the whole**
    - **Reconcile duplicates across partitions **

    

- Validations

  Their evaluation shows that they can maintain high deduplication ratio and balance resource consumption, space saving, and throughput .

------

**Strengthens**

- Large scale study implies some findings and insights.
- System design for primary dataset has a good performance comprehensively (RAM frugality, low CPU utilization, low disk usage, sustained deduplication throughput).

------

**Weaknesses**

- This paper doesn't solve problem about too small chunk size. However, small chunks disappear in the experiments' results. 

------

**Comments**

- Partitioning by workload type and analyzing different workload datasets is worth learning.
- Analyzing dataset from different aspects is worth learning.