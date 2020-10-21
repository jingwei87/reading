**Title:** **DADI: Block-Level Image Service for Agile and Elastic Application Deployment**

**Source:** **ATC'20**

**Authors:** Huiba Li, Yifan Yuan, Rui Du, Kai Ma, Lanzheng Liu, and Windsor Hsu, Alibaba Group.

---

**Background**

Nowadays, rapid application is achieved through effective process-based virtualization and layered image systems, containers (such as docker). And such solution include: 

- Image, which is equivalent to a root file system, such as the official image ubuntu:16.04. 

- Container: The relationship between the image and the container is like the class and instance in object-oriented programming. The image is a static definition, and the container is the entity of the image at runtime. 

- Repository: The repository can be regarded as a code control center for storing images. The image is stored as a File-System-Based Remote Image mode.
- layer: The image is composed of layers. Each layer presents different cluster of file.

**Summary**

- What is the *problem* the paper aims to solve?

  The deployment process of an application first needs to pull the image from the repository if there don't have cache file in local, and then create a new container after decompression. This will lead to a lot of time for pulling and decompressing the image, and we usually only need a small part of the image to start an container. However, the existing solutions do not support downloading part of image files. Therefore, how to download some files from a remote respository to speed up the startup of the container is the problem to be solved in this article.

- How can the paper address the problem? What is the main idea of this paper?

  The main reason why we can't pull only part of the image file is that the image file is stored as layers. Every layer is a compressed and stored as  a whole. So if our operation instance is block, we can request data blocks on demand. This is the high-level idea of this paper, from this idea, the author gives the following designs.

  - **Block-Level Image** 

    DADI models an image as a virtual block device while retaining the layered feature. Each layer is  viewed a collection of modified data blocks under the file system and corresponding to the files added, modified or deleted by the layer. Under virtual block device is file system, DADI can support selective file system to store blocks. The raw data is organized into  variable-length segments, each segments has an index which stores segment's offset, length and so on.

  - **Merged View of Layers**

    In practice, each layer contains roughly the same number of segments M. When we look up a segment in a layer, the time complexity is logM if we use binary-search. The whole look up time complexity increase linearly with the number of layers n. So if we merge all layers' seggment index, the time complexity of index look-up is log(nM). 

  - **Compression and Online Decompression** 

    As talked above, DADI use block-level image,  so they also designs a compression file frmat called ZFile for chunk-level store.  Using ZFile, the logic offset of on-demond segments can easily translate into block's offset in file system.

  - **Writable layer** 

    For image building, DADI propose the writable layer which consists of one file for raw data and one for index. Both of these files are open-ended, and are ready to accept appends. As overwrite occur in the writable layer. They also use parallel for quickly image building.

  -  **P2P Data Transfer**

    The application deployment is usually thousands of concurrent, so DADI use P2P for quickly download. DADI uses a tree topology to facilitate management and maintenance.

    

- Validations

  Their evaluation shows that both cold startup or warm startup time, DADI are faster than traditonal. 
  
  They also prove that their data path is more reasonable and their download and compression algorithm speed is higher.
  
  

---

**Strengthens**  

- Compatible with existing algorithms and schemes while implementing a new system. For example, it supports other arbitrary lower-level file systems while implementing block-level, or it supports other multiple decompression algorithms while implementing ZFile.
- A complete acceleration engine is proposed, which shortens the cold start of containers to a few seconds, and provides a second-level pull-up capability for large-scale container cluster expansion.

---

**Weaknesses**  

- The layer merge algorithm is not clear and has some issues which are not described. For example,    if there exists segment of two different which offset and length is same, how to solve is  not stated.
- Although the compression and decompression speed are higher, but the size of compressed chunks is larger. And everything about space occupied between ZFile and traditional syatems is  not declared.
- The keyword "blob" is not explained and hard do understand.

---

**Comments**  

- The paper comes from the business environment, the whole framework, the designs in detail and the actual effect are very good. Its high level idea is derived from the previous solutions in VMIamge, I think it is worth learning to make new designs on existing ideas and apply them to existing problems. However, judging from the structure of the whole article, its organization is a bit messy, and it needs to constantly cross chapters to find the reasons for doing this or to find the proof of this part, which will make readers understand a little difficult.