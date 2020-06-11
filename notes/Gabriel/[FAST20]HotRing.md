**Title:** **HotRing: A Hotspot-Aware In-Memory Key-Value Store**

**Source:** **FAST'20**

**Authors:** Jiqiang Chen, Liang Chen, Sheng Wang, Guoyun Zhu, Yuanyuan Sun, Huan Liu, and Feifei Li.

---

**Summary**

- The problem the paper aims to solve

  The traditional solution for in-memory indexing is using hash index with collision chains. Hotspot issue is serious with such data structure when the request burst happens. But hotspot-awareness is being overlooked in in-memory key-value stores. The design with no hotspot-awareness makes the system perform poorly on highly skewed workloads.

- How can the paper address the problem? What is the main idea of this paper?

  - Hash index with the ordered-ring structure is presented to enable fast memory access of hotspot instead of traversing the whole collision chain. Both tag and key are used for fast ordering and lookup termination.
  - Random header movement and statistical sampling strategy are designed to identify hotspots and adjust header differently. The former strategy works well for a single hotspot, which has less accuracy but faster reactions. The latter performs better for multiple hotspots and highly skewed workloads.
  - Lock-free rehashing is also supported to adapt to the workload changes concurrently.

- Validations

  They conduct the experiments with chain-based hashing and FASTER to validate their work. HotRing outperforms other approaches by more than 2.1X. HotRing can even achieve significant improvements since it can identify and adjust hotspots while other approaches cannot.

---

**Strengthens**  

- The paper motivates the problem of hotspot awareness and adjustment that is being overlooked in the past.
- It is clever to change the collision chain into ordered-ring to reduce memory access overhead and adjust head pointer easily for hotspots.

---

**Weaknesses**  

- Applying lock-free methods for concurrency lacks novelty. This method is well adopted by other works already, which makes rehashing and accessing not be able to become an independent contribution.
- Taking one bit of tag for hash key may affect the algorithm. But it doesn't give any explanations or possible outcomes. 
- The section of concurrent operation is hard to follow. Its figures and writing do not provide successful examples and illustrations, which makes it confusing and hard to understand.

---

**Comments**  

- Lock-free designs are adopted by other approaches in order to achieve higher performance, such as FASTER. This makes its contributions hard to become an independent one.
- It occupies the highest bit of tag for rehashing. What if the tag value is affected, then the order may be changed. How does it affect the algorithm? No explanation is provided.