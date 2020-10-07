**Title:** **DupHunter: Flexible High-Performance Deduplication for Docker Registries**

**Source:** **ATC'20**

**Authors:** Nannan Zhao, Hadeel Albahar, Subil Abraham, and Keren Chen, Vasily Tarasov, Dimitrios Skourtis, Lukas Rupprecht, Ali Anwar and Ali R. Butt.

---

**Summary**

- The problem the paper aims to solve

  The layer-level deduplication cannot exploits redundancy across layers in current docker design. However, compressed layer cannot provide good deduplication ratio and deduplicating uncompressed files across layers will also worsen the `GET` latency  when user is pulling. Therefore, this paper tries to provide a balance between layer fetching latency and deduplication ratio on uncompressed layers.

- How can the paper address the problem? What is the main idea of this paper?

  - **Replica deduplication mode**

    It is possible to provide high performance and reduce `GET` latency overhead by utilizing the method of combining replicas and deduplication. **P-servers** are responsible for storing layer replicas and its corresponding manifests while **D-servers** are for decompression and deduplicating on layer replicas for reliability.  

  - **Parallel layer preconstruction**

    Layer can be built in parallel. In this way, it can minimize the latency of layer rebuilt and assembled from deduplicated files significantly. However, compressed layer concatenation cannot be processed in parallel.

  - **Prefetch layer cache and preconstruct cache** 

    P-servers have in-memory cache for prefetching layers while D-servers have on-disk cache for preconstructing layers. It uses **ARC** and predict from the user's perspective.

- Validations

  Their evaluation shows that they can save space by up to **6.9x** under erasure coding scheme or they can reduce latency by up to **2.8x** with **B-mode 3**.
  
  They also prove that their layer prefetching cache and layer preconstructing cache works as it expected.
  
  

---

**Strengthens**  

- **DupHunter** is capable to provide the balance between deduplication and pull latency by setting policy or using selective mode.
- This paper has reasonable motivation and observation to support their design, which makes this paper practical and convincing. 

---

**Weaknesses**  

- Metadata takes about 0.6% in their evaluation, which may incur large amount of memory consumption. This situation may make it impossible to deploy on the real servers, like Docker Hub.
- The paper only stats that **DupHunter** can utilize selective deduplication mode for heavily skewed workloads. But no relevant evaluations are provided, such as how the threshold in selective mode affects system.
- The parameters about S-mode have no explanation and statement.

---

**Comments**  

- S-mode sounds tricky but this paper doesn't provide any information about it, not even about how to set its parameters. The readers cannot verify its correctness by simply checking the evaluation.
- The design of **DupHunter** is intuitive and solid but there are so many small mistakes in the paper.
- This paper doesn't provide any evaluation compared with the raw docker registry. It only compares with Bolt, which is insufficient.