---
layout: post
title:  "Note: iDedup: Latency-aware, inline data deduplication for primary storage."
date:   2021-03-22 12:00:00
categories: PaperReading
tags: [PrimaryStorage, Deduplication]
---

## Reference

> Kiran Srinivasan, Tim Bisson, Garth Goodson, Kaladhar Voruganti. [iDedup: Latency-aware, inline data deduplication for primary storage](https://www.usenix.org/legacy/event/fast12/tech/full_papers/Srinivasan.pdf). In Proc. of FAST 2012.

## What

An inline deduplication solution iDedup for primary workloads, while minimizing extra IOs and seeks.
<!-- more -->

## Background

* Offline dedupe
  * First copy on stable storage is not deduped
  * Dedupe is a post-processing/background activity
* Inline dedupe
  * Dedupe before first copy on stable storage
  * Primary: Latency should not be affected!
  * Secondary: Dedupe at ingesting rate (IOPS)!
* Spatial locality
  * Dropping in deduplication ratio is less than linear with increasing block size.
  * Duplicated data is clustered
* Temporal locality
  * Dropping in deduplication ratio is less than linear with decreasing fingerprint table size (deduplication index)
  * Duplicate data is written repeatedly close in time.
  
## Paper Summary

Existing work does not implement inline deduplication for primary data. The performance challenges are the key obstacle because overheads (CPU + I/Os) for both reads and writes hurt latency. By exploring the CIFS network traces (ATC'08 Measurement), they found that spatial locality exists in duplicated primary data, and temporal locality exists in the access patterns of duplicated data.

Based on the spatial locality findings, iDedup performs deduplication only for sequences of disk blocks to avoid fragmentation; based on the temporal locality, iDedup keeps smaller dedupe metadata as an in-memory cache to avoid extra I/Os.

Finally, they do experiments for latency-sensitive primary workloads, show that iDedup has limited CPU impact (<5%), small latency impact (~4%), and achieve the max deduplication ratio of about 60%.

## Strength

* First introduce inline deduplication for primary data.
* Achieves high deduplication ratio with limited CPU and latency overhead.
* Analysis of the CIFS trace to find the `Spatial locality` and `Temporal locality` in primary data deduplication.

## Weakness

* Using pre-set parameters for iDedup rather than the dynamic threshold.
* Near-exact deduplication, loss of some storage efficiency for inline deduplication.

## Comments

* CIFS network trace: include file access pattern and file size (better than S3Trace), but currently not found.
* Could we do variable-size chunking for primary data deduplication to improve deduplication ratio while not introduce significant latency?
  