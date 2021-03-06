---
layout: single
title:  " [운영체제] Multi Level page Tables  "
---

Multi-Level Page Tables
===
페이지 테이블의 크기를 작게 관리하기 위해 고안된 방법입니다. 32비트 컴퓨터 기준으로 4MB의 페이지 테이블이 필요한데, 응용 프로세스가 메모리를 많이 사용하지 않더라도 페이지 테이블은 4MB가 제공됩니다. 이 때, 사용되지 않고 낭비되는 페이지 테이블 엔트리가 존재하게 됩니다. 이를 해결하기 위해 고안된 방법이 Multi-Level Page Tables 입니다.

Bigger pages
---
32bit addressing 을 기준으로 16KB 페이지의 크기를 만들기 위해 14 비트의 offset 을 사용합니다. 이렇게 되면 총 18비트의 VPN 을 사용하게 되고, 페이지 테이블의 크기는 1MB가 된다. big pages 들은 각각의 페이지 안에서 internal fragmentation 문제가 발생합니다. 할당된 페이지의 크기는 크지만 막상 그 안에서 사용하는 메모리양은 적을 경우가 많기 때문입니다. 그러므로 현재 대부분의 시스템들은 상대적으로 작은 페이지의 사이즈를 사용합니다. 

Hybrid pages (paging and segments)
---
새그먼트마다 페이지 테이블이 한개씩 존재합니다. 또, 세개의 base, bounds 쌍이 존재합니다. 여기서 base 와 bounds 는 새그먼트의 것이 아닌 페이지 테이블의 base 와 bounds 를 의미합니다.     
* base register : 페이지 테이블의 시작주소를 가리킵니다 
* bounds register : 페이지 테이블의 맥시멈값을 가리킵니다 (valid 한 것 중 가장 높은 곳을 가리킵니다.)    
    
**linear page table 에 비해 페이지 테이블에서 할당되지 않는 페이지가 수가 줄어들고 페이지 테이블의 크기가 더 작아집니다.**

단점
---
* 힙과 같이 sparsely used 하면 페이지 테이블이 낭비되게 됩니다. 
* External fragmenetation 이 발생합니다. 
* 페이지 테이블의 크기가 가변적으로 될 수 있고, 이는 메모리에서 free space 를 찾는 복잡한 작업이 요구되는 단점이 있습니다.


multilevel page tables
---
page directory 와 page table 구조로 계층적으로 페이지 테이블을 관리합니다. 예를들어 PDBR 이 물리 페이지 주소 200번 (디렉토리 주소) 을 가리킨다고 했을 때 그 안에 201~204 번의 PFN을 갖는 PDE 가 4개가 들어가 있고, 각 PFN에 1개라도 valid 한 엔트리가 존재한다면 디렉토리의 PFN의 valid 를 유효하게 설정합니다. valid한 201번 PFN을 들어가면 또 4개의 엔트리가 존재합니다. 4개의 엔트리 중에는 valid 한 엔트리가 존재해야 합니다. 

address translation
---
VPN -> TLB 검색 -> TLB miss -> 페이지 디렉토리 인덱싱 -> Valid 여부 체크 -> 페이지 테이블 인덱싱 -> memory access

단점
---
time space trade off : space 관점은 개션됐지만 time 관점에서 시간이 더 오래걸리는 단점이 있습니다.
complexity : TLB miss 인 경우 복잡한 연산을 해줘야 하므로 overhead 가 발생합니다. 

Addressing
---
addres space 가 16KB, page 의 사이즈를 64바이트로 가정하겠습니다. 이는 14비트 addressing 으로 8비트는 VPN 6비트는 offset 으로 구성된다는 것을 유추할 수 있습니다. 각각의 PTE 는 바이트입니다. 즉 현재 페이지에는 16개의 페이지 엔트리가 존재하게 됩니다. 8비트의 VPN은 4비트씩 분할해 앞선 4비트는 디렉토리 인덱스, 뒤의 4비트는 테이블 인덱스를 가지게 됩니다. 

참고
---
페이지 디렉토리의 베이스를 가리키기 위한 CP3 가 존재합니다.    
32비트의 경우 32비트 어드레싱을 사용합니다. 한개의 페이지 크기는 4킬로 바이트를 사용합니다(12비트 사용). 나머지 20비트는 10비트 디렉토리, 10비트 테이블 로 인덱싱되어 사용합니다. 10비트씩 인덱싱 됐으므로 각 페이지의 엔트리는 1024개가 됩니다. 64비트로 오면 9비트씩 인덱싱이 되고 각 페이지마다 512개의 엔트리를 갖습니다.     
64 비트 컴퓨터는 48비트 어드레싱을 이용합니다. 그 이유는 64비트 어드레싱을 할 경우 너무 많은 페이지 디렉토리가 필요하게 되고 이것은 메모리 낭비로 이어질 수 있기 때문입니다. 하지만 페이지 크기는 여전히 4킬로바이트 페이지를 사용합니다.      
* 32비트 어드레스 스페이스 -> 4기가 바이트
* 64비트 어드레스 스페이스 -> 48비트 어드레싱을 하고있습니다. 커널 영역은 128 테라바이트 유저 영역도 128 테라바이트 만큼 커집니다. 

