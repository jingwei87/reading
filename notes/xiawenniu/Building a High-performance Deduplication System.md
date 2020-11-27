**Title:** **Building a High-performance Deduplication System** 

**Source:** **ATC'11**

**Authors:** Fanglu Guo    Petros Efstathopoulos

---

**Summary**

- The problem the paper aims to solve:

  **Deduplication efficiency**: how well the system can detect and share duplicate data units

  **Scalability**: the ability to support large amounts of raw storage with consistent performance.

  **Throughput**: the rate at which data can be transferred in and out of the system

  **Reference management**: who use what, when to reclaim unused segment.

  This paper takes the four aspects into account.

- How can the paper address the problem? What is the main idea of this paper?

  - **Progressive Sampled Indexing**  

    Instead of full index, this paper uses sampled index, which puts a part of (1/T) index into  the memory and others in disk, and pre-fetch the FPs of the container *hit FP* belongs to. Additionally, as number of segments in disk grows, we can dynamically change ratio of index in memory and in disk to support scalability.

  - **Group mark-and-sweep**

    Based on *sweep-and-mark (aka Garbage Collection)*, *Group sweep-and-mark* just marks the changed *backup groups* and sweep them to free unused segment.

  - **Client-Server Interaction**  

    This part is not so important in the total paper. Based on our asynchronous RPC protocol, we have implemented an event-driven client pipeline, where each backup step is implemented as a separate pipeline stage. Meanwhile, when calculating FP of segment, put multi-thread into consideration.

- Validations

  **Throughput:** The paper uses same dataset in its prototype and normal file system to trace throughput,   and throughput in this prototype is much higher.
  
  **Deduplication efficiency:** the paper use different dataset to calculate ideal capacity and real capacity to prove little deduplication efficiency sacrifice. 
  
  **Scalability:** the paper use the same dataset in system empty and system close to full capacity   to trace throughput in different system condition to prove nearly consistent performance.
  
  **Reference management:** the paper proves that reference management throughput is proportional to data backed or deleted. 
  
  

---

**Strengthens**  

- The system takes four aspects into consideration and construct a total system.
- This paper has reasonable motivation and observation to support their design, which makes this paper practical and convincing. 

---

**Weaknesses**  

- Sacrifice a little deduplication accuracy to achieve throughput and scalability.
- The third part, **Client-Server Interaction** is somewhat unnecessary and  conventional 

---

**Comments**  

- Sacrificing just little deduplication is unbelievable.
-  **Client-Server Interaction** is likely to fill the article content but unnecessary.
