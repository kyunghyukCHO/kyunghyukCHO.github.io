---
layout: single
title:  " [운영체제] Translation Lookaside Buffer  "
---

Translation Lookaside Buffer 
===
줄여서 TLD라고 부릅니다. paging 의 느린 속도를 개선하기 위한 방법입니다. 이 때, 하드웨어적으로 어떠한 것을 지원해야하며 운영체제의 담당에 대해 알아보아야 합니다.    
* CPU's memory management unit (MMU) : 모든 메모리에 관련하여 CPU 가 해줘야 하는일을 우리는 mmu 라고 부릅니다.   
mmu는 일반적으로 하드웨어 입니다. 하지만 단독적으로 동작할 수 없고 운영체제가 mmu가 정의해놓은 것들을 설정합니다. 이 mmu 는 address translation 에 대한 cache 역할을 합니다. 자주 접근하는 메모리에 대한 memory translation 에 대한 결과를 캐시, 즉 TLB에 저장합니다. 

Address Translation Example
---
8비트 어드레싱 + 16바이트 페이지를 가정합니다. 상위 4비트는 VPN을 저장하고 하위 4비트로 offset 저장 합니다. 4바이트 정수값 array[10] 이 있고 시작 virtual address 는 100번지로 가정합니다. VPN 6 번지의 04바이트 부터 array가 채워져있는 버츄얼 페이지를 보여줍니다. VPV 6번지의 array[0]은 접근한 적이 없으므로 TLB miss 가 발생합니다. 페이지 테이블 내에서 해당 VPN이 가리키는 offset 으로 PTE를 읽어오고 그 내용을 TLB에 넣고 retry 를 진행합니다. 하지만 array[1]에 접근할 때  VPN 6번지의 PFN은 전과 동일합니다. 그러므로 TLB hit 가 됩니다. 즉 페이지 테이블에 접근하지 않습니다. 하지만 VPN 7번에 다시 접근한다면 다시한번 TLB miss 가 발생합니다. 

TLB miss
---
* hadware-managed TLB : 하드웨어는 메모리에 페이지테이블이 어디에 위치해 있는지 PTMR을 통해 알 수 있습니다. PTBR에는 페이지 테이블의 베이스 주소를 저장하고 있습니다. 대신 이 PTBR에 대해 정확한 값을 넣어주는 역할은 운영체제가 담당합니다. 
* software-managed TLB

TLB contents
---
* fully associative
    * any given translation 는 TLB 의 아무공간에 위치할 수 있습니다
    * 하드웨어는 이러한 엔트리들을 병렬적으로 탐색합니다. 
* VPN,PFN,other bits
    * valid bits, protection bits, address space identifier, dirty bit, etc...

Context switches
---
TLD에 들어온 정보들은 어떤 프로세스인지 운영체제가 서로 구분하기 어렵습니다. 그러므로 context switch 가 일어날 때마다 valid bit 를 invalid 하게 해주는 TLB flush 를 해주어야 합니다. 하지만 이는 큰 overhead 를 발생시킬 수 있고, 효율적으로 페이지테이블에 접근 하기 위한 장점이 의미없게 됩니다. 이 문제점을 해결하기 위해 TLB에 address space identifier (ASID) 를 추가로 넣어줍니다. context switch 가 일어날 때 마다 ASID 로 구분하면서 접근합니다. 

sharing a pages
---
사용되는 물리메모리의 수를 줄여주고 메모리 overhead 를 감소시키기 위한 방법으로 page 를 sharing 할 수 있습니다. virtual address 상으로 다른 VPN을 가진 값이 같은 PFN을 가리킬 수 있습니다. 하지만 protection bit 은 read-executable only 해야 합니다. 

Replacement Policy
---
TLB에 모두 valid 한 translation 이 들어가 있을 때, 추가적인 엔트리가 들어온다면 기존에 있던 엔트리 중 하나를 빼내고 새로 추가되어야 합니다. 이때 빠져나가는 엔터리는 **Least-Recently-Used (LRU)** 알고리즘 기반 혹은 random 하게 빠져나가게 됩니다. 

LRU
---
최근에 사용되지 않은 엔트리를 선택합니다. 하지만 예를들어 TLB 의 사이즈가 n 이고 n+1번의 메모리 접근이 반복적으로 발생한다면 TLD miss 가 계속 발생하는 단점이 있습니다.