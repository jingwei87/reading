**Title:** **MapX: Controlled Data Migration in the Expansion of Decentralized Object-Based Storage Systems**

**Source:** **FAST2020**

**Authors:** Li Wang,Yiming Zhang,Jiawei Xu

---

**Summary**

- What is the *problem* the paper aims to solve?

  CRUSH is a useful and efficient placement algorithm in centralized Object-basede storage system.It has high scalabitiy,robustness and I/O performance.But CRUSH-based storage systems suffer from uncontrolled data migration when expanding the clusters, which will cause significant performance degradation.

- How can the paper address the problem? What is the main idea of this paper?

  They reference the centralized placement methods and  extend the original CRUSH algorithm with an extra time-dimension mapping and a "layer" thinking.

  - Layer:each expansion is viewed as a new layer of the CRUSH map represented by a virtual node beneath the CRUSH root.
  - Timestamps:use an extra dimensional mapping for controllable data migration in the expansion.

- Validations

   The MAPX is designed to improve CRUSH's disadvangtage,so its evaluation compares I/O,latency,compution cost and affected PGs with CRUSH.From the results,MAPX improves above indexs obviously.However,from the idea of the paper,we can infer that it's loadbalance is from a integral point, that's to say,the new layer maybe is not balance at a particular time and whether the point will influence is not mentioned.

---

**Strengthens**  

- MAPX doesn't change CRUSH tree and the time-dimension mapping with minimum modifications to CRUSH,it keeps CRUSH's advantages.
- It combines centralized placement methods' advantages,just intrducing layer and timestamps, through which it improves CRUSH's disadvantage and obtains good performance.
- MAPX provides administrators with the ability to control the migration at the algorithm level.

---

**Weaknesses**  

- Its application scenarios are limited, MAPX is not suitable for general object stores(which is nontrivial to maintain and retrieve the timestamps of arbitrary objects)so that it will need more depth work if we want to use it in some reality scenarios .
- The section of related work occupies large spaces, but in section of MAPX, some concepts don't express clearly such as if the remapping could influence loadbalance.

---

**Comments**  

- In my views, MAPX is an optimization of CRUSH,it improves CRUSH's performance but it has some others' problem such as remapping's loadbalance at the same time.And if the new layer just has two cabs so it need to chose old layer,in which will it break the balance of old layer?
