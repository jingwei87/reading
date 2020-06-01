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

RAIDP, a combined method of replication and erasure coding which achieves similar failure tolerance as three replicated systems while performed better when writing new data and generated small overhead during data updates.
<!-- more -->

## Paper Summary

Modern datacenter uses many storage systems as redundancy to store data due to disks failure, which is an inevitable event in practice. Erasure coding can provide data redundancy with lower additional storage space overhead, but its performance is not friendly to frequently accessed data (Warm data). So a most modern system like GFS/HDFS uses replicate to store warm data, which is better for load balancing, parallelism, reduce sync latency. Compare to erasure coding, the replica could avoid the CPU and network usage for encoding/decoding/repair.

This work combines replication and erasure coding to make the 2-replica with intra-disk erasure coding achieve quickly recover from two simultaneous disk failures like 3-replica.

It divided each of the N disks into N-1 Superchunks. Set 2 replica for each Superchunk on a different disk, while makes any two disks share at most 1 Superchunk. To get benefits from erasure coding, RIADP adds a "disk add-on" (Lstor) for each disk to store the XOR result of all Superchunks in that disk. For only one disk lost, the user could restore the data from the other replica; For any two disk lost, the user could restore some of the Superchunks by the other replica (due to two disk only share at most 1 Superchunks), and restore the other Superchunks via erasure coding (do some XOR operations and transfer some Superchunks from another disk).

This work implements the RAIDP based on HDFS, and conduct a complete and convincing experiment to evaluate the performance compared with 3-replica HDFS and 2-replica HDFS to show that the performance is between the two types of HDFSs. And compare the Superchunk recovery procedure with RAID-6 on a network basis.

## Strength

* The paper is very well written, and the analysis of each design point is very full.
* Simple idea achieves obvious effects and significantly reduces costs compare with 3-replica and improve performance compare with erasure coding.

## Weakness

* Lack of some experimental analysis of network traffic and other resource utilization during Superchunk recovery.
* This paper assumes that all disks are a single network node, this operation may affect the measurement of data recovery overhead.

## Comments

* Table 2 missed a "sec" unit for RAID-6 result.
* RAIDP itself assumes that each disk is a network node. This assumption is different from the actual situation (single storage server usually has an array of multiple hard disks), and how will the impact of Superchunk's restoration proposed in this article be affected?
* Compared with the RAIDP solution, what effect will it have when using 2-replica directly and using erasure coding technology inside each storage node?
  