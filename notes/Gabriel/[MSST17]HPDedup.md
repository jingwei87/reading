**Title:** **HPDedup: A Hybrid Prioritized Data Deduplication Mechanism for Primary Storage in the Cloud**

**Source:** **MSST'17**

**Authors:** Huijun Wu, Chen Wang, Yinjin Fu, Sherif Sakr, Liming Zhu, Kai Lu

---

**Summary**

- The problem the paper aims to solve

  Deduplication for primary storage in clouds.

  Primary storage often requires low latency for deduplication. Therefore, inline deduplication or post-processing deduplication is utilized to make this happen. However, in-line deduplication highly relies on the temporal locality of workloads to deduplicate. However, mixed streams often destroy the locality, which results in low deduplication ratio.

- How can the paper address the problem? What is the main idea of this paper?

  - **Deploy deduplication mechanism in hypervisor** 
    - Allow to check duplicates between multiple virtual machines
    
  - **Estimate locality and prioritize cache for high locality workloads**
  - Use reservoir sampling to choose elements equally 
    - Apply the unseen algorithm to estimate locality based on samples provided by reservoir sampling
  - Adjust threshold for utilizing spatial locality and latency for different workloads
    

- Validations

  Their evaluations show that they can improve inline deduplication ratios, cache hit ratio and cache distribution to prove their effectiveness of estimation of LDSS for mixing streams.
  

---

**Strengthens**  

- **HPDedup** has an interesting problem and also show the effectiveness of using estimation and corresponding cache replacement.
- **HPDedup** utilizes many other new techniques to complete their work such as using unseen and D-LRU.

---

**Weaknesses**  

- Using estimation methods lacks analysis and enough motivation. Why not just using counting mechanism? This explanation should be included in the paper.
- Optimizing fragmentation is not very convincing as well as the peak disk capacity requirement. 

---

**Comments**  

- None (See strengthens and weaknesses)

