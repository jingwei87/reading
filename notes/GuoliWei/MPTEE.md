**Title:** **MPTEE: Bringing Flexible and Efficient Memory Protection to IntelSGX**

**Source:** **EuroSys ’20**

**Authors:** Wenjia Zhao, Kangjie Lu, Yong Qi and Saiyu Qi 

---

**Summary**

- The problem the paper aims to solve

  - The application of SGX has a fundamental need of flexible memory protection that, while preventing itself from being attacked, can update memory-page permissions dynamically.

  - Existing systems can not provide the flexible and efficient memory-protection mechanism due to the lack of hardware support and the untrustedness of operating systems.

- How can the paper address the problem? What is the main idea of this paper?

  - **Observation**

    In enclave, the memory sections that have same permissions are adjacent.

  - **Cross-Region Bound Check**(CRBC)

    (1) Use three bound registers (R/W/X) to offer six overlapping permission region (X/R/RX/RW/RWX/No).

    (2) By changing the boundary value of three registers, the ranges of memory permission regions can be flexibly changed at runtime.

  - **Adaptive Permission Enforcement**

    While one permission region has no change requirement at runtime, they configure the SGX permission bits in *config.xml*, relying on hardware check to reduce the performance overhead of CRBC.

  - **Memory Isolation & Control-data Integrity**

    (1) Save all the code and data related to the permission enforcement in the isolated no-permisson region offered by CRBC. 

    (2) Use a trampoline table to check indirect calls and jumps.

- Validations

  They extensively evaluate MPTEE from performance, utility, and effectiveness. 

  The results of experimentation and analysis show that MPTEE can achieve a flexible and secure memory protection while just imposing a small overhead (avg 6.6%. With *Adaptive Permission Enforcement*, the overhead caused by CRBC can be improved more efficiently).

---

**Strengthens**  

- The observation that  in enclave, the memory sections that have the same permissions are adjacent.
- The MPTEE actually develop a  flexible and secure memory-protection mechanism with less hardware support and just impose a small overhead.

---

**Weaknesses**  

- In 2018, Intel MPX has been eliminated by Microsoft. 
- The paper argues that as the hardware component, SGX cannot be easily upgraded. Old version SGX will still be the dominating version for a while. And they think that for enforcement integrity, MPTEE’s design is compatible with new SGX.

---

**Comments**  

- Intel MPX itself has many deficiencies (such as maintenance burdens), but this paper does not mention the solutions MPTEE has taken to address these deficiencies.
- 

