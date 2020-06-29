**Title:** Strong and Efficient Consistency with Consistency-Aware Durability

**Source:** FAST‘20

**Authors:** Aishwarya Ganesan, Ramnatthan Alagappan, Andrea Arpaci-Dusseau, and Remzi Arpaci-Dusseau, University of Wisconsin—Madison

---

**Summary**


> - Asynchronous durability model  can provide high performance but weak consistency while synchronous durability model can support strong consistency but low performance. There is no a model which can enable strong consistency while delivering high performance. 
>
> - The paper proposes a novel approach to durability called Consistency-Aware Durability(CAD), which can enable strong consistency while delivering high performance. Further, the authors implement CAD and cross-client monotonic reads in a system called ORCA by modifying ZooKeeper. ORCA applies many novel techniques to achieve high performance and strong guarantees. For example, a durability-check mechanism efficiently separates requests that read non-durable items from those that access durable ones. Next, a lease-based active set technique  ensures monotonic reads while allowing reads at many nodes. Finally, a two-step lease-breaking mechanism helps correctly manage active-set membership.
>
> -  Durability-check mechanism: When the read request reaches the leader node, the leader node compares the update-index and durable-index corresponding to the key. If the update-index is found to be less than the durable-index, it means that all the content of this key has been synchronized to majority of nodes. So,the leader directly serve read requests. If update-index is found to be greater than durable-index, all logs before update-index are forced to be synchronized to majority of nodes of the member group and then durable-index and update-index need to be updated. When the read request reaches the follower node, if the update-index is found to be less than the durable-index, the read request is directly served; otherwise, the read request is redirected to the leader node for processing.
>
>    Lease-based active set technique: When the leader intends to make a data item durable
>   (before serving a read), it ensures that the data is persisted and applied by all the members in the active set. Only nodes in the active set are allowed to serve reads.
>
>    Two-step lease-breaking mechanism:  First, a disconnected follower marks itself out of the active set; the leader then removes the follower from the active-set. 
>
> 
>

---

**Strengthens**  

> + For applications that can tolerate data loss after synchronization to most member groups, the method mentioned in the paper can provide a perfect cross-client monotonic read.
> + In addition, for distributed storage systems deployed in multiple data centers, the method mentioned in the paper can also effectively reduce the read delay across data centers.

---

**Weaknesses**  

> + Read requests sended to follower nodes will make engineering implementation complex and increase the possibility of system error. 
> + Furthermore, the new system has a low performance when read occurs immediately after write.  

---

**Comments**  

> + Followers and leader must send messages to each other and make synchronous durability, which costs a lot.
> + The new system will degrade to strongly consistent ZooKeeper when read occurs immediately after writing which just provides low performance. Meanwhile, the new system fit more read and less write but more read and more write. 
