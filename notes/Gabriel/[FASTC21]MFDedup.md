**Title:** **The Dilemma between Deduplication and Locality: Can Both be Achieved?**

**Source:** **FAST'21**

**Authors:** Xiangyu Zou, Jingsong Yuan, Philip Shilane, Wen Xia, Haijun Zhang, and Xuan Wang

---

**Summary**

- The problem the paper aims to solve

  Fragmentation in deduplication leads to poor restore throughput and garbage collection performance (especially targeting backup workloads). Some researchers try to alleviate this phenomenon via rewriting or caching. However, fragmentation still cannot get solved and hurts performance.

- How can the paper address the problem? What is the main idea of this paper?

  - **Use OPT layout**
    - **OPT** layout keeps read amplification to 1, which introduces no fragmentation.
    - **OPT** layout records all inner chunk relationships with other backup versions.
    - However, these relationships lead to **O(2^n)** of categories, which is unacceptable.

  - **Focus on neighbor backup version only to reduce unacceptable numerous categories**
    - Most duplicate chunks are from the previous version (not from the previous of previous version).
    - An only small fraction (less than **0.5%**) of duplicate chunks are either from the current version or the previous version.

  - **Arrange chunks to update OPT layout to keep optimal**
  - Focus on an active category to migrate duplicate chunks referenced by other versions of backup workloads.
    - Keep update the **OPT** layout as storing backups.

- Validations

  Their evaluation shows that they can improve actual deduplication ratios (1.12-2.19× higher) and restore throughput (2.63-11.64X higher) than **HAR** and **Capping**.
  

---

**Strengthens**  

- **MFDedup** has a solid core problem, which inspires the following motivation and presents the corresponding challenges. The writing is very clear and easy to follow.
- The derivation relationship of backups supports the whole idea of **MFDedup**. Ignoring **skip** duplicate chunks is smart and acceptable.

---

**Weaknesses**  

- The paper does not explain why seek number is fixed to 20. Maybe its implementation leads to this number. It makes readers hard to understand and guess about it.

---

**Comments**  

- It is worth learning the methodology that pinpointing the core problem first and addressing the corresponding challenges.

