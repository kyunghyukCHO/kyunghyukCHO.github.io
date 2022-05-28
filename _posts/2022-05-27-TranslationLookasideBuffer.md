---
layout: single
title:  " [운영체제] Translation Lookaside Buffer  "
---

Translation Lookaside Buffer 
===
줄여서 TLD라고 부릅니다. paging 의 느린 속도를 개선하기 위한 방법입니다. 이때, 하드웨어적으로 어떠한 것을 지원해야하며 운영체제의 담당에 대해 알아보아야 합니다.
이런 답으로 TLB 입니다. CPU's memory management unit (MMU) : 모든 메모리에 관련하여 cpu 가 해줘야 하는일을 우리는 mmu 라고 부릅니다. mmu는 일반적으로 하드웨어 입니다. 하지만 단독적으로 동작할 수 없고 운영체제가 mmu가 정의해놓은 것들을 설정합니다. 이 mmu 는 address translation 에 대한 cache 역할을 합니다. 자주 접근하는 메모리에 대한 memory translation 에 대한 결과를 캐시 즉 TLB에 저장합니다. 

예시
---
8비트 어드레싱 + 16바이트 페이지를 가정합니다. 
즉, 상위 4비트는 VPN을 저장하고 하위 4비트로 offset 을 합니다. 
4바이트 정수값 array[10] 이 있고 시작 virtual address 는 100번지 입니다. 
VPN 6 번지의 04바이트 부터 채워져있는 버츄얼 페이지를 보여줍니다. 
VPV 6, array[0] 번지는 잡근한 적이 없으므로 TLB miss 가 발생합니다. 페이지 테이블 내에서 해당 VPN이 가리키는 offset 으로 PTE를 읽어오고 그 내용을 TLB에 넣고 retry 를 진행합니다. 하지만 array[1] 의 VPN 6번지의 PFN은 전과 동일합니다. 그러므로 TLB hit 가 됩니다. 즉 페이지 테이블에 접근하지 않습니다. 하지만 VPN 7번에 다시 접근한다면 다시한번 TLB miss 가 발생합니다. 