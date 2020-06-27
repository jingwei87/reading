**Title:** **MapX: Controlled Data Migration in the Expansion of Decentralized Object-Based Storage Systems**

**Source:** **FAST2020**

**Authors:** Li Wang, Yiming Zhang, Jiawei Xu


---

**Summary**

- What is the *problem* the paper aims to solve?

   In CRUSH, each OSD (object storage device) is assigned a weight, and the load of OSDs is usually determined by these weights. When expanding a cluster in an object-based storage system using the CRUST algorithm, the weight of the new OSD will cause the balance among the original weights to change due to the addition of a new OSD. For example, there were originally three OSDs with weights of 1. Each of them stored 4 objects when they reached load balancing. At this time, if a new OSD with a weight of 1 is added. According to the weight of these four OSDs, they should store 3 objects each. This will cause each of the original three OSDs to migrate an object to the new OSD for new load balancing. These OSDs belong to different clusters, so this kind of migration is often global. This is called uncontrolled data migration. Due to the data migration, I/O delay will be caused, and IOPS will be reduced, resulting in reduced performance.

- How can the paper address the problem? What is the main idea of this paper?

  CRUSH is an algorithm used to calculate on which OSD an object is stored in a decentralized object-based storage system. It contains two steps. The first step uses hashing and modulo to map objects to PG (placement group). The second step is to map from PG to OSD. In the second step, it includes the steps of selecting root, cabinet, rack, and OSD. In each selection, it is determined by the weight and the utilization rate of the equipment.

  In centralized object-based storage systems, a central directory is used to store the location of each object. To avoid migration, new objects are stored in new OSDs while keeping old OSDs unaffected. Inspired by this idea, to isolate the new OSDs from the old OSDs, they introduced the concept of layers represented by a virtual node beneath the CRUSH root. To distinguish between old and new layers, they introduced timestamps and used timestamps for subsequent mapping.

  When the cluster is expanded, they regard the new cluster (which can be OSDS, shelves, cabinets) as a new layer. At the same time, new PGs are introduced (note that each PG has two timestamps, the static timestamp, and the static timestamps. The two timestamps are initialized to be the same as the layer's timestamp, and the dynamic timestamp can be changed). In subsequent calculations, MAXP firstly compares the object's timestamp with the PG's static timestamp to map the new PG. Then MAPX uses dynamic timestamp mapping to the corresponding layer. The steps in the layer are similar to CRUSH's second step.

  After the introduction of layers and timestamps, when the cluster expands, new objects are placed on the new layer, the old objects and layers are not affected, so cluster migration without migration can be achieved. When the workload changes, only need to change the dynamic timestamp of PGs to remap the PG. If you want to achieve cluster shrinking, you can use it as the reverse process of cluster expansion. Firstly you assign all PGs in the layer to be shrunk to the remaining layers according to aggregated weights and then migrate the PGs to the target layers through remapping. When the clusters need to be merged, only the time stamp of the cluster needs to be changed.


- Validations

   Due to the introduction of the timestamp idea, this paper chose ceph-RBD（adding timestamp in rbd_header structure) and ceph-FS (having inode in its inode) for implementation. The algorithm in this paper is designed to improve CRUSH's disadvantage, so its evaluation compares I/O, latency, computation cost and affected PGs (PGs that need to be remapped when data migration occurs) with CRUSH. From the results, it is improved in performance compared to CRUSH.

---

**Strengthens**  

- Optimized based on the original CRUSH algorithm, MAPX can be used MAXP without major modifications in the actual application of CRUSH scenarios.


- They apply the existing "new Objects to new OSDs" separate processing idea to the existing CRUSH, to transform a global data migration problem into a local original mapping problem. The performance has been greatly improved from the evaluation results, This idea is worthy of reference.


---

**Weaknesses**  

- Its application scenarios are limited, MAPX is not suitable for general object stores (because the timestamp is required) so that it will need more depth work if we want to use it in some real scenarios.
- The section of related work occupies large spaces, but in the section of MAPX, some concepts don't express clearly such as if the remapping could influence load balancing.

---

**Comments**  

- In my view, MAPX is an optimization of CRUSH. It improves CRUSH's performance but it has some others' problems such as remapping's load balance. Secondly, if the new layer just has two cabs so it needs to choose the old layer, in which will it break the balance of the old layer?
