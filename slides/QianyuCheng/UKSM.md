**Title:** UKSM: Swift Memory Deduplication via Hierarchical and Adaptive Memory Region Distilling

**Source:** FAST'18

**Authors:** Nai Xia, Chen Tian, Yan Luo, Hang Liu Xiaoliang Wang

---

**Summary**

- The *problem* the paper aims to solve
  - Existing methods for Content Based Page Sharing (CBPS) ignored the dynamic information during memory content modification.
  - CPU resource were assigned to stable and unstable duplicated pages equally, so the stable ones could not be sufficiently treated.

- How can the paper address the problem? What is the main idea of this paper?
  - Pages present similar deduplication patterns in the same region.
    - The expectation is that more CPU resource are used for reclaiming static-duplicated pages.
    - The region with high duplication ratio and copy-on-write ratio will be "promote" to next level with high scan speed.
  - Bacause hash comparison among similar pages need high hash strength to avoid the collision, partial page hashing need to be adaptive.
    - Adapt hash strength to make *Benefit* between time consumption of page hashing and byte-to-byte page comparison being maximum value locally.
    - Progressive hash alogorithm can reduce the computation complexity based on previous hash value.

- How does the paper validate the systems/algorithms?
  - Deduplication efﬁciency analysis in different types of workload like static workload with no rewriting and workload with rewriting of the share pages.
  - Analyse the hash algorithm in regions with low and high redundancy.
  - Besides, validation from the real world benchmarks and performance impact to the workloads of performance evaluation.
  - The evaluations shows that UKSM are with higher deduplication efficiency than KSM and low performance impact.

---

**Strengthens**  

- UKSM got remarkable page scanning performance improvement from the baseline performance of KSM and achieve the balance of page hashing and byte-to-byte page comparison.
- UKSM have low background CPU usage, which means that the hosts can run other intensive workloads with UKSM smoothly.

---

**Weaknesses**  

- The UKSM was modified from KSM source code, and will limited by KSM page scanning method.
- As a general memory deduplication system, UKSM only handles anonymous pages for containers.
- CPU consumption spiking at the beginning may conflict with other application and lead the host to shut down.

---

**Comments**  

The paper make a set of strong experiments to prove the outstanding performance of UKSM, and the idea of hierarchical distilling can be used in our work. But in particular, the code of UKSM is stored as patch format and not straightforward for readers to understand, and there are many typos in their paper.