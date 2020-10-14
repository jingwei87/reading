**Title:** OSCA: An Online-Model Based Cache Allocation Scheme in Cloud Block Storage Systems

**Source:** ATC'20

**Authors:** Yu Zhang, Huazhong University of Science and Technology; Ping Huang, Huazhong University of Science and Technology and Temple University; Ke Zhou and Hua Wang, Huazhong University of Science and Technology; Jianying Hu, Yongguang Ji, and Bin Cheng, Tencent Inc.

**Summary**


> - The problem the paper aims to solve
>
>   Existing equal-allocation-to-all-instances policy in cache server of cloud storage storage system has a relatively low hit ratio, which leads to a  high IO traffic and overall inefficiency.
>
> - How can this paper solve this problem?
>
>   OSCA employs three techniques: Re-access Ratio Based Cache Model, the total hit traffic metric as optimization target and Configuration Searching.
>
>     >- **Re-access Ratio Based Cache Model(RAR-CM)**
>     >
>     >RAR-CM is a noel cache model which uses the amount of unique data blocks between two consecutive accesses to the same data block as reuse distance and the amount of unique blocks accessed from the time it enters the cache to the time it is evicted from the cache as the eviction distance. When reuse distance is smaller than eviction distance, it means that it is a miss to cache and the request needs to visit the back-end HDD storage. Counting reuse distance of every block can draft reuse distance distribution, after which we can obtain the miss ratio curve(MRC).
>     >
>     >- **optimization target** 
>     >
>     >Based on the obtained miss ratio curve(MRC), an optimization target can be built like following function: E=sum(Hit-Ratio*Traffic),where *sum* means add-up of all nodes.
>     >
>     >- **Searching for Optimal Configuration**
>     >
>     >Based on the cache modeling and defined target mentioned above, OSCA searches for the optimal configuration, where dynamic programming is used in it.
>
> - validations
>
>   The evaluations show that  OSCA based on RAR-CM can reduce I/O traffic to back-end storage server by 13.2% due to equal-allocation-to-all-instances policy.
>
> 
---



**Strengthens**  

> + OSCA has a higher hit ratio to cache and a lower IO traffic to the back-end storage server.
> + OSCA can implement on-line algorithm and meanwhile has a low memory footprint and time complexity.
> + It can achieve a suitable trade-off between computation complexity and space overhead.

---

**Weaknesses**  

> + This paper hides some important details deliberately, for example, how to use DP in searching for optimal configuration is not discussed in detail in the paper.
> + Whether the MRC is close to ideal curve is mystery and the paper doesn't show corresponding evidences. 

---

**Comments**  

> + This paper hides important details deliberately, making me doubt it authenticity. 
> +  Parts of optimization target and configuration searching is simple in this paper, which maybe can not guarantee a high performance.
