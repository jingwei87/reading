**Title:** **DistCache: Provable Load Balancing for Large-Scale Storage Systems with Distributed Caching**

**Source:** **FAST'19**

**Authors:** Zaoxing Liu and Zhihao Bai, Johns Hopkins University; Zhenming Liu, College of William and 
Mary; Xiaozhou Li, Celer Network; Changhoon Kim, Barefoot Networks; Vladimir Braverman 
and Xin Jin, Johns Hopkins University; Ion Stoica, UC Berkeley.

---

**Summary**

- What is the *problem* the paper aims to solve?

  Existing storage systems usually consist of multiple storage clusters, and each storage cluster consists of multiple storage servers. Due to the unbalanced access, the load of the servers in the existing storage cluster is unbalanced. The previous work adopted a new cache node in each cluster to cache n servers' the hottest O(N log N) objects in the entire cluster, which has solved the load imbalance between the storage servers in each cluster. But the problem of load imbalance between clusters still exists.

- How can the paper address the problem? What is the main idea of this paper?

  Naturally, a big cache node can be added above m clusters to cache the clusters' O (M logM) data items. Due to high drawback of a single large node throughput, splitting a big node into a cache layer composed of multiple cache nodes is more feasible. Now, next to solve is: how to allocate cache, how to query data items, and how to update with coherence.

  - **Independent hash functions for cache allocation** 

    Existing solutions replication and partition have contradictions in scalability and cache synchronization. This paper uses two independent hash functions respectively for up and bottom cache layer for cache allocation.

  - **Power of two choices for query**

    Up and bottom cache nodes report their own load information, clients first use the same hash functions as cache allocation to find cache node, then choose the lower load layer cache node for query.    

  - **Two phases for update** 

    •In first stage, storage server notifies all cache nodes that have data to be update are invalid. After the first phase, update its primary copy, and send an acknowledgment to the client.

    • In second stage, update all cache nodes.

- **Validations**

  Chose switch-based caching as usage scenarios. Use two physical switches and two servers as an experiment setup. Use 100 million objects as dataset. Through vary load skewness, cache size, and write ratio, proved that Distcache performs well from throughput aspect.
  

---

**Strengthens**  

- The power of two choices is naturally decentralized and efficient. The theory guarantees that the maximum load of the machine is much lower than Random.
- The idea can be used for multiple cases, such as in-memory caching and switch-based caching.

---

**Weaknesses**  

- Mathematical proof about bipartite graphs is obscure.
- Lack of support from actual data in experiments and problems.

---

**Comments**  

- The power of two choices makes every node not be overloaded, which makes a “life-or-death” improvement in this problem, instead of a “shaving off a log n” improvement. And the thought can also be scaled, "the power of K choice". Simply idea generates strong power.

  
