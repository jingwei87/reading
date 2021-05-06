---
layout: post
title:  "Note: Improving restore speed for backup systems that use inline chunk-based deduplication."
date:   2021-04-28 12:00:00
categories: PaperReading
tags: [Deduplication]
---

## Reference

> Mark **Lillibridge**, Kave Eshghi, Deepavali Bhagwat. [Improving restore speed for backup systems that use inline chunk-based deduplication](https://www.usenix.org/system/files/conference/fast13/fast13-final124.pdf). In Proc. of FAST 2013.

## Problem Statement

* Slow restore performance due to chunk fragmentation
    * Store chunks according to the order of appearance of unique chunks.
    * Restore the newer snapshot, the more random reads, the lower the performance.

<!-- more -->

## Literature Summary

This paper provides a restore performance measurement method: container read per MB restored, which is the main source of expenses in the restore process. 
It simply addresses the restore performance issues by using the forward assembly area to take the place of the LRU cache. The main idea of the FAA is to load each container only once for each restore slice (Fill all the chunks from the container in the slice at one time). Then, they designed a container rewrite method, which performs deduplication to the income segment with the top T best old containers. It finds only part of the duplicate chunks but bound the container read per MB to T-related numbers.

## Results

* The less memory is used, the better the effect of FAA relative to LRU. When the memory size is not limited, the performance is the same.
* FAA is less affected by container size than LRU.
* The rewrite approach could bound the container read per MB to (T+5)/20, where container size is 4 MB, the segment size is 20 MB.
