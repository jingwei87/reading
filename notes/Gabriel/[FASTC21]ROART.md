**Title:** **ROART: Range-query Optimized Persistent ART**

**Source:** **FAST'21**

**Authors:** Shaonan Ma, Kang Chen, Shimin Chen, Mengxing Liu, Jianglang Zhu, Hongbo Kang, and Yongwei Wu.

---

**Summary**

- The problem the paper aims to solve

  - B^+^-Tree and radix tree do not support variable-sized keys and have relative low range queries performance.
  - Current allocators can be improved to adopt the poor write scalability in persistent memory.

- How can the paper address the problem? What is the main idea of this paper?

  - **Propose Range queries support based on ART**
    - Make leaf node support variable-sized keys
  - **Optimize ROART**
    - Leaf compaction
    - Entry compression
  - Selectively metadata persistence
    - Minimally ordered split
  - **DCMM**
    - Propose a new allocator to improve performance
    - Enable instant restart and fast recovery

- Validations

  Their evaluations show that **ROART** outperforms the stat-of-the-art B^+^-Trees by 1.17∼8.27X respectively.
  

---

**Strengthens**  

- **ROART** enables range query support while achieves better performance compared with B^+^-Tree. This could enlighten complicated optimization in future.
- This paper proposes a delayed check memory management which enables fast recovery and instant restart. At the same time, it can outperform other allocators greatly. It will show the great impact in the future.

---

**Weaknesses**  

- The overall idea in this paper is normal and everywhere, which makes it less innovative. 
- The paper writing shades too many details, making it hard to follow and understand.
- Evaluations do not compare the time used by recovery with other methods. 

---

**Comments**  

- This work is highly incremental and lack of innovation. I would reject this paper very weakly if I were a reviewer. 

