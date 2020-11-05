**Title:** **PACEMAKER: Avoiding HeART attacks in storage clusters with disk-adaptive redundancy**

**Source:** **OSDI ’20**

**Authors:** Saurabh Kadekodi, Francisco Maturana, Suhas Jayaram Subramanya, Juncheng Yang, K. V. Rashmi, Gregory R. Ganger

---

**Background**
- To guarantee data storage reliability, data redundancy is a common-used method that stores auxiliary 'parity chunks,' providing the ability of data recovery.

- Storage clusters are made up of disks from a mix of makes/models acquired over time, and different makes/models have highly varying failure rates.

- Storage clusters employ a "one-size-fits-all-disks" approach to choosing redundancy levels.

- Hence, space overhead is often inflated by overly conservative redundancy levels to ensure sufficient protection for the most failure-prone disks in the cluster.

- [Tuning redundancy schemes to observed disk failure rates](https://www.usenix.org/system/files/fast19-kadekodi.pdf) can provide balance between space-saving and data-protection.

**Summary**

- The problem the paper aims to solve:

  - The IO load of transitions between redundancy schemes overwhelms the storage infrastructure and makes current design proposals unusable, called transition overload.
  
  - Since prior designs are reactive, for an increase in AFR(a parameter used to measure the hard disk condition), the data is already under-protected by the time the transition to increase redundancy is issued. Moreover, it will continue to be under-protected until that transition completes.

- How can the paper address the problem? What is the main idea of this paper?

  - **Longitudinal production trace analyze**
  
    - Analyzing 5.3 million HDDs from several clusters, two kinds of disks are divided, trickle-deployed disks(slow rise) and step-deployed disks(massive disks at once).
    
  - **Causes of transition overload**
  
    - For the trickle-deployed disks, it needs time to make enough disks identified as in need of transition.
  
    - For the step-deployed disks, all of a large number of disks should be transitioned.
    
  - **Other conclutions**
    
    - The expected fraction of disks failure rises gradually over time with no clear wear out.
    
    - The useful life of disks could have multiple phases.
    
    - The infancy of disks is often short-lived.
  
  - **Proactively initiate transitions**
    
    - To solve the problems above, the most effective way is to initiate transitions proactively.
    
    - The first component of PACEMAKER, called Proactive-transition-initiator, is designed to determine when to transition the disks.
    
    - For the disks employed in infancy(both trickle-deployed and step-deployed), a transition to a more space-efficient scheme is performed when infancy is over.
    
    - For trickle-deployed disks, the first-deployed small number of disks is used to observe the AFR curve of the same mark/model. Hence the same model disks deployed later will be transitioned proactively.
    
    - For step-deployed disks, when a configurable fraction of the tolerated-AFR of the current redundancy scheme is met, initiates a proactive transition.
    
  - **More factors while selecting redundancy schemes**
  
    - PACEMAKER call groups of disks using the same redundancy as Rgroup. The component to determine which redundancy scheme should be applyed to a group of disks is called Rgroup-planner.
    
    - To avoid transition overload, IO constraints should be met when the Rgroup-planner estimates whether the transition to a scheme is worth it.
    
    - If all the constraints are met, the Rgroup-planner chooses the one that provides the highest space-savings.
    
    - For trickle-deployments, the Rgroup-planner creates a new Rgroup for a redundancy scheme if and only if one does not exist already to prevent too many small-sized Rgroups.
    
    - For step-deployments, the Rgroup-planner creates a new Rgroup for each step-deployment, even if there is already one or more Rgroups that employ the chosen scheme.
    
  - **New transition techniques**
  
    - The last component to choose the transition technique, execute the transition via a rate-limiter is called transition-executor.
    
    - In addition to conventional re-encoding, PACEMAKER supports two new approaches to changing the redundancy scheme for disks, depends on the fraction of the Rgroup being transitioned at once.
    
    - If a small percentage of a Rgroup's disks are being transitioned, retain the transitioning disks' contents, and simply move (copy) data to other disks within the current Rgroup.
    
    - If a large fraction of disks in a Rgroup need to transition together, it is more efficient to transition the entire Rgroup rather than only the disks that need a transition at that time.
    
    - Almost always, trickle-deployed disks use Type 1 because they transition a-few-at-a-time, and step-deployed disks use Type 2 because Rgroup-planner maintains each step in a separate Rgroup.
    
    - Rate-limiter limits each transition to the peak-IO-cap within its Rgroup, to prevent transition overload caused by concurrent transitions.
    
    - Rate-limiter arbitrarily slowing down a transition to minimize interference is only possible when the transition is not in response to a rise in AFR. 

**Implementation of PACEMAKER in HDFS**

A prototype of PACEMAKER is implemented for the Hadoop distributed file system (HDFS). Because the components required for the transition-executor are already present and adequately modular. It's quite easy to implement PACEMAKER in HDFS.

---

**Evaluation**

PACEMAKER -enabled disk-adaptive redundancy using is evaluated on production logs from four large-scale real-world storage clusters. Analysis for four large real-world storage clusters from Google and Backblaze show 14–20% average space-savings while transition IO is kept small (<0.4% on average) and bounded (e.g., <5%).

---

**Strengthens**  

- PACEMAKER performed well, solved the problem entirely, and achieved a balance between space-saving and preventing transition overload.
- Because multiple phases of disk life are determined and used, PACEMAKER contributed more space-saving than past design.

---

**Weaknesses**  

- This design is only for the case where storage clusters are designed for a single dedicated storage service, consisting of a large number of disks. It can't be used on multiple different distributed storage services or a smaller number of disks.
- PACEMAKER needs a better way to set parameters, such as threshold-AFR and peak-IO constraints, to prevent possible failure.

---

**Comments**  

The research in this article is based on the author's previous research. In fact, it solves the remaining problems that caused the previous design to be unable to be applied in the actual production environment. Although it does solve the problem perfectly, The new design is not so much brand new as it is a supplement to the old design.

