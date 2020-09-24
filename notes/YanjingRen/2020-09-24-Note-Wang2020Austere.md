## Reference

> Wang Qiuping, Li Jinhong, Xia Wen, Kruus Erik, Debnath Biplob, Lee Patrick PC. [Austere Flash Caching with Deduplication and Compression](https://www.usenix.org/system/files/atc20-wang-qiuping.pdf). In Proc. of USENIX ATC, 2020.

## What

A flash cache mechanism for accelerating HDD based on deduplication and compression with less memory overhead.

## Background

* Flash Cache: Accelerate HDD storage by caching frequently accessed blocks in flash (SSD/PM).
* LBA: chunk address in HDD

<!-- more -->

## Paper Summary

Flash cache can significantly improve the IO performance of the disk system. However, space efficiency and endurance remain a critical challenge due to Nand flash itself (Cost per GB, rewritable times). Deduplication and compression could be saving storage space and I/O operation via remove duplicate content, but incur substantial memory overhead for index management. 

This paper builds AustereCache to support memory-efficient indexing while preserving data benefits of deduplication and compression.

The main idea of AustereCache is hashing to partition index and cache space which use the **prefix index** in the memory and the complete index in SSD to reduce the memory index space overhead (Tracking compressed lengths of chunks in SSD, not memory); Use the **bucket-based cache scheduling** replaces the scheduling for entire cache to optimize the efficiency of multi-threaded access; Combined with **compression** and  **deduplication** to reduce the storage space of data in the cache.

In practice, they use four bucket based data structures to locate cached data:

* In memory
  * LBA (chunk address in HDD) index: LBA-hash prefix  | FP hash | Flag
  * FP index: FP-hash prefix | Flag
* In SSD
  * Metadata region: FP | List of LBAs
  * Data region: Chunk
  
The prefix is used extensively instead of the full hash index here to saving memory space. Data deduplication and compression are performed while inserting new data in the cache. Simple use of compression to chunks (e.g., 32KB) will lead to variable-size output results. The output results are divided into subchunks (if insufficient, padding to fill the fixed-length subchunk, e.g., 8KB).
To optimize cache scheduling, they use the least-recently-used policy for LBA-index, least-referenced policy(based on CM-Sketch to count the reference) for FP-index in each bucket. Each LBA-index bucket is divided into two parts, old and recent, and ordered according to FP hash's reference count (meaning that each change needs to be sorted and moved as a whole) to address recency without memory overhead.

The evaluation shows that the AustereCache prototype saves 69.9-97.0% of memory usage than CacheDedup and Nitro while maintaining comparable read hit ratios and write reduction ratios and achieving high I/O throughput.

## Strength

* Compared with the existing cache deduplication scheme, the memory overhead is significantly reduced.
* The experiment is complete and convincing.

## Weakness

* The overall throughput seems to be much lower than the speed that SSD can achieve (for SATA, about 550MB/s), and also lower than the speed of HDD (about 150MB/s). Why is the throughput lower after adding the cache?
* The size of the index structure is directly related to the hard disk's total size for caching.
* FP-index has a one-to-one correspondence with the metadata region and data region in SSD, and the space saved by deduplication and compression is difficult to use.
*  Use the slice+padding method to divide the compressed variable-length chunk into n fixed-length subchunks, which wastes part of the flash space.

## Comments

* Is it possible to replace the existing slice+padding method with a compression technology with a determined output length to achieve the same effect without wasting space? Reference: Gao Xiang, Dong Mingkai, Miao Xie, Du Wei, Yu Chao, Chen Haibo. [EROFS: A Compression-friendly Readonly File System for Resource-scarce Devices](https://www.usenix.org/system/files/atc19-gao.pdf). In Proc. of USENIX ATC, 2019.