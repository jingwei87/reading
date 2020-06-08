---
layout: post
title:  "Note : RAIDP: replication with intra-disk parity"
date:   2020-05-01 12:00:00
categories: PaperReading
tags: [FileSystem]
---

## Reference

> Eitan Rosenfeld, Aviad Zuck, Nadav Amit, Michael Factor, Dan Tsafrir. [RAIDP: replication with intra-disk parity](https://dl.acm.org/doi/abs/10.1145/3342195.3387546). In Proc. of EuroSys, 2020.


## What

RAIDP, a combined method of replication and erasure coding, which achieves similar failure tolerance as three replicate. The performance is better than 3-replica when writing new data, and keeps almost the same read performance as 3-replica. The recovery time is 14x less than RAID-6 on a network basis.
<!-- more -->

## Paper Summary

Disk failure is one of the main problems in the storage system. Erasure coding provides redundancy with low storage overhead, but introduce high-performance overhead for data write, read, and recover (mainly used for cold data); Replica waste storage space, but have benefits for load balancing, read performance and recover performance (mainly used for warm data). The main problem is how to quickly recover from two simultaneous disk failures like 3-replica, but without restoring to a third replica for warm data?

The main idea is using a larger data management unit Superchunk for 2-replica, and distribute the Superchunks to make any two disks share at most 1 Superchunk while no same Superchunks on a single disk. In this case, the storage system could handle up to 2 disks failure for most Superchunks (Only one unrecoverable Superchunk per disk). Then, add a disk add on (Lstor, device that fails separately from disk) for each disk to store the parity of the disk. When two disks fail, the remaining Superchunks could recover by computing erasure coding for Lstor and recovered Superchunks.

In practice, it divides each of the N disks into N-1 Superchunks. The Lstor stores parity is just XOR result for all Superchunks stores on the disk (Because when 2 disks fail, each failed disk at most has one Superchunk need to recover by erasure coding).  The RAIDP method can improve write performance by reducing 3 times write sync to 2, and incurs small performance overhead for data update thanks for the Superchunks layout.

This work implements the RAIDP based on HDFS (simulated Lstors by DRAM) and conducts experiments to evaluate the read, write, update performance and CPU, network usages compared with 3-replica HDFS, and 2-replica HDFS by Hadoop standard, terasort, and wordcount benchmarks (Intel HiBench). It shows that RAIDP writes performance higher than 3-replica HDFS and lower than 2-replica HDFS while keeping almost the same read performance as 3-replica HDFS. It also compares the Superchunks recovery with network-based RAID-6 and shows RAIDP achieves a 14x shorter recovery time cost because all parity computes localy.

## Strength

* The paper is very well written, and the analysis of each design point is very full.
* Simple idea achieves obvious effects: significantly reduces network traffic, power consumption, storage space costs compare with 3-replica, and improves data recovery performance compare with erasure coding.

## Weakness

* Lack of some experimental analysis of network traffic and other resource utilization during Superchunk recovery.
* The basic requirements for dividing Superchunks and the impact of Superchunks size on performance limit the upper limit of available storage space for each disk. In evaluation, the 16-node cluster only gets ~800GB available storage space with 6GB Superchunks.

## Comments

* Table 2 missed a "sec" unit for RAID-6 result.
* The RAIDP design describes various designs of Lstor, but the experiment is only based on RAM simulation. Although it meets the design goals of Lstor, it has a huge impact on performance.
* How does expanding the size of Superchunks affect I/O and recovery performance?
