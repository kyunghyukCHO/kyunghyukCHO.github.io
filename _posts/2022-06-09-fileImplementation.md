---
layout: single
title:  " [운영체제] File System Implementation "
---

File System Implementation
===
File system Interface 들이 실제로 내부구조와 어떻게 작동하여 구현되는지에 대해 포스팅 하겠습니다.      
* File system 은 순수한 소프트웨어 입니다. 
* 즉, CPU 나 Memory 가상화와는 달리 추가적인 하드웨어들의 도움이 없습니다.
 
Overall Organization
---
* Blocks
    * divide the disk into blocks
* Data region
    * fixed portion of the disk for these blocks
    * 8 blocks (metaData) + 56 blocks (Data)
* Metadata
    * 각각의 파일의 Track 정보 입니다.
    * inode (index node)
        * metadata 를 저장합니다.
        * disk 의 일정 부분(5 blocks of 8 blocks) 을 inode table 로 구성합니다.
* Allocation structures
    * inodes 혹은 Data blocks 가 free 한지 혹은 할당되었는지 알기 위해 사용됩니다. (2 blocks)
    * Free list of bitmap 
* Superblock
    * 전체 file system 의 정보를 포함합니다. (1 block)
    * inode 와 data blocks 들의 갯수 혹은 시작점 그리고 끝점 등을 저장합니다.
    * File system 을 mounting 할때, 운영체제는 superblock 을 우선적으로 읽습니다.

inode
---
예를들어, Block size 를 4KB 라고 하겠습니다. 64 blocks 가 있으므로 총 256 KB partition 을 갖습니다. inode 의 사이즈가 256B 라고 한다면 한개의 block 안에는 16 개의 inodes 가 들어갈 수 있고 total 80개의 inode 를 갖습니다. 

i-number 
---
각각의 inode 들은 내부적으로 number 로 참조되어 있습니다. i-number 를 제공받으면 inode 가 위치한 disk 의 위치를 직접적으로 계산할 수 있습니다.    
* inode number 32 
    * offset into the inode : 32 * sizeof(inode) = 8KB
    * start address of the inode table on disk : 12KB
    * 8 + 12 = 20KB
* Disk 는 byte 단위로 읽지 않고 Sector 단위로 읽습니다. 즉, 해당 inode 를 읽기 위해서는 sector 번호를 알아야 합니다. 
    * 하나의 sector 는 보통 512B 입니다.
    * sector address : (20*1024) / 512 = 40 

Multi-level index
---
inode 는 고정된 수의 direct pointers 와 한개의 indirect pointer 를 가지고 있습니다.
* indirect pointer
    * Data block 을 가리키지만, 해당 Data block 안에는 다시 Data block 을 가리키는 pointer 포함합니다.
    * 만약 file 이 충분히 커지면 indirect block 이 할당됩니다.
    * Double indirect pointer 는 충분이 큰 files 에 대하여 지원합니다.

Directory Organization
---
Directory 도 일종의 file 처럼 관리됩니다. inode 에 regular file 대신 "directory" 로 marking 됩니다. Direct pointer 를 타고 Data block 을 들어가면 entry name, i-number 쌍을 저장하고 있습니다. 추가로 record length 정보와 file name 의 length 정보를 가지고 있습니다. 만약 file 지울 때 Data 를 모두 초기화 하는 작업은 overhead 를 발생시킬 수 있습니다. **그리하여 i-number 를 0으로 바꾸는 작업만 수행합니다.** 만약 새로운 entry 가 이전에 사용했던 entry 의 length 를 넘지 않는다면, 해당 entry 를 재사용하게 됩니다. 

Rading a File from Disk
---
* open("/foo/bar", O RDONLY)
    * 우선 file /foo/bar 의 inode 를 찾습니다.
        * Root directory 의 inode 를 읽습니다
        * foo 의 entry 를 찾고 foo 의 i-number 를 찾습니다
        * foo 의 inode 를 포함하고 있는 block 을 읽고 bar 의 inode 를 찾습니다 
* bar 의 inode 를 memory 로 읽습니다. 
* permission check 
* file descriptor 를 할당합니다. 
* read()
    * file 의 첫번째 block 을 읽어와 inode 정보를 얻습니다.
    * indoe 의 access time 을 update 합니다. 
    * file offset update 
    * read pointer 를 4KB 증가시킵니다.
* close()
    * file descriptor 를 반납합니다
* write()
    * Data block 에 write 합니다.
    * 만약, 새롭게 file 을 open 한다면 data block 을 새로 할당해주고 inode 를 update 해야합니다.
        * data bitmap 을 읽습니다
        * bitmap 을 update 합니다
        * inode 를 읽어오고 update 합니다
        * 실제 data block 에 write 작업을 합니다.

Caching and Buffering
---
reading 과 writing 작업은 매우 비싸고 disk 에 많은 I/O 를 발생시킵니다. 운영체제는 이러한 문제를 해결하기 위해 page cache 를 관리하고 있습니다. 

* Page cache
    * 자주 읽는 data block 에 대해 cache 에 그 정보 (ex. inode and data)를 저장합니다. 
* Write buffering
    * write 정보를 buffering 하여 delay 시키고 한번에 update 합니다.
    * disk scheduling 이 효과적 입니다.
    * 같은 영역에 대해 여러번 write 이 발생할 시 최신 과정만 update 하면 됩니다.

문제점
---
cache 와 buffer 은 memory 에 지니고 있습니다. 즉, momory 에 한시적으로 가지고 있을 때 전원이 꺼지면 disk 의 정보와 memory 정보 간에 불일치가 발생합니다. 즉 update 가 되지 않습니다. 