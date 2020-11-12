**Title:** Dsync: a Lightweight Delta Synchronization Approach for Cloud Storage Services

**Source:** MSST 2020

**Authors:** Yuan He  Lingfeng Xiang Wen Xia  Hong Jiang  Zhenhua Li Xuan Wang and Xiangyu Zou

---

**Summary**

#### What is the *problem* the paper aims to solve? Can you explain the problem clearly?

+ **Delta** **synchronization** had to match data chunk by sliding a search window byte by byte.

+ **CDC** technique solve the ‘boundary-shift’ problem,but cause extra compute overhead .
+ We require **lightweight** delta sync.
+ This paper proposed Dsync, which included FastFp(fast weak hash) and redesigning the delta sync protocol(deduplication)



#### How can the paper address the problem? What is the main idea of this paper?

+ OLD:
  + ***rsync***: the window will slide further byte by byte,mainly process in client
  + ***WebR2sync+***: client is a webbrowser,mainly process in server
  + ***CDC***: The matching windowof these two methods needs to slide from the beginning to the end of a ﬁle byte by byte, comparing every byte in the worst case.So these paper want to use **CDC**,but **CDC** technique remains attractive because it greatly simpliﬁes the chunk ﬁngerprinting and searching process.Also it may fail to eliminate redundancy among similar but non-duplicate chunks.

+ NEW:

  + **FASTCDC**: This paper propose to use **FASTCDC(ATC16)**,and replace SipHash with SHA1 in WebR2sync+ because it's more reliable.**

  + **FASTFP**:the CDC technique incurs extra calculation even though FastCDC is very fast.Since CDC is sliding on the data by using the rolling hash algorithm, this paper use the weak hash according to the rolling hashes during CDC.These addition operations are so lightweight as not to inﬂuence the overall performance of FastCDC.

  + **Client/Server Communication Protocol**

    + Only **FastFP**(without strong hash) will be sent to the serevr at first.If the weak hash is match,the client will calculate the strong hash.
    + Also they use the **big chunk**,the continuous match chunk will be merged as a big chunk and just compare the big chunk's strong hash.

    

#### Validations

+ **FastFp** reaches almost 400-460MB/s, about 50% higher than Adler32.

+ **Dsync** runs 1.5×-3.3× faster than the state-of-the-art rsync based WebR2Sync+ and Dedue-based approach.

---

**Strengthens**  

+ In this paper, parts of fastCDC is used to generate FASTFP, which greatly saves the cost of hashing calculation.

---

**Weaknesses**  

+ Both deduplication and FASTCDC are existing things.

+ The main contributions of this paper are pieceing them together and proposing the fastFP hash algorithm.

---

**Comments**  

+ The overall design of the article is not complicated, the writing is clear and fluent, and the testing part is also relatively perfect