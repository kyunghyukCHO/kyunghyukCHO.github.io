---
layout: single
title:  " [운영체제] Segmentation "
---

배경
===
Address space 가 물리메모리에 연속된 공간으로 있다고 가정한다면, 여러가지 단점들이 발생합니다.     
* Address space 에 할당받았지만 사용하지 않는 메모리 공간 발생
* 큰 Address space 를 제공하고자 할 때 물리적 메모리의 부족 현상
    * 64 bit 컴퓨터는 한 프로세스에게 2^64 바이트의 Address space 를 할당해야하고 이는 물리적 메모리에 Address space 가 연속적으로 배치될 때 현실적으로 불가능하다는 것을 알 수 있습니다. 

**즉 낭비되는 공간이 크고 큰 Address space 들을 제공하기 어렵습니다. 이를 해결하기 위해 Segmentation 개념이 등장했습니다.**

Segmentation
===
Address space 를 Portion ( ex) 코드 스택 힙.. )단위로 Segment 하여 그 portion 단위로만 물리적 메모리에 연속적으로 위치시킵니다. Segment 들은 각각 Base 와 Bounds 가 존재합니다. 이 방법은 물리메모리에 사용되지 않는 Address space 가 존재하는 것을 방지할 수 있습니다. 오직 사용되는 메모리만 물리 메모리에 relocation 됩니다. 

Address Translation
---
앞선 장에서 Address translation 할 때, 물리적으로 연속된 공간에 Address space 가 있다고 가정하여 offset 하였습니다. 하지만 Segmentation 이 된다면 한 address space 내의 code, heap, stack 등은 각각의 base 값과 bound 값을 가지므로 각 base 값으로부터의 offset을 해주어야 합니다. segment 한 portion 밖의 메모리 주소를 load 하고자 하면 메모리 exception (segment fault) 이 발생합니다.    

**하지만, 어느 영역의 base 주소를 기준으로 offset을 해야하는지에 대한 논의가 필요합니다.**  

Explicit approach
---
14비트 addressing 을 했다고 가정하면 이는 2^14 바이트 만큼의 address space 를 만들 수 있습니다. 이때 앞선 2 비트를 segment 를 표현하는데 사용합니다. 
* 00 - code
* 01 - heap
* 11 - stack 

Stack Segmentation
---
Stack segmentation 의 경우 Address translation 이 그대로 적용되기 어렵습니다. 높은 번지가 base 주소가 되고 낮은 번지수로 자라나기 때문입니다.     
아래는 Stack segmentaion 의 Address Translation 예시 입니다.      
14비트 주소 : 1 1 1 1 0 0 0 0 0 0 0 0 0 0 
* 맨 상위 2개의 비트 11 : Stack 영역을 의미합니다.
* 맨 상위 2개의 비트를 제외한 12개의 비트로 인해 2^12, 4KB 의 스택영역을 갖습니다.
* Offset :  3KB - 4KB = -1KB 
* 물리적 주소 : 28KB - 1KB = 27KB
* 절댓값 -1KB 는 2KB (새그먼트의 현재 사이즈 ) 보다 작으므로 Protection 하다고 할 수 있습니다.

Support for sharing
===

Code sharing 
---
메모리를 saving 하기 위해서 특정 메모리 segment를 여러 Address space 들을 위해 공유하여 메모리를 효율적으로 사용할 수 있습니다. 
단, 공유할 수 있는 segment 는 read only 합니다. 다른 프로세스가 해당 새그먼트에 write을 한다면 다른 프로세스의 address space에
영향을 끼칠 수 있기 때문입니다. 때문에 세그맨트들은 각각 protection 정보들을 가지고 있어야 합니다. 

Fine grained vs Coarse grained
---
아마 좀 더 효울적인 메모리 구성을 위해 fine grained 한 segment 를 구성하는 것이 나은 방법이라고 생각할 수 있습니다. 하지만 이는 각각의 base 와 bounds 들이 생겨나고 레지스터들이 낭비될 수 있습니다. 그러므로 각각의 CPU마다 segmentation 의 최대 수를 명시해놓고 운영체제는 한 address space를 표현할 수 있는 한정되어 있는 segment 의 갯수로 fine 과 coarse 를 결정합니다. 

OS support 
---
* context switch : address space 에서 사용한 segment 레지스터들을 저장하고 환원시킵니다.
* 물리 메모리의 free 한 공간을 관리합니다. 새로운 address space 가 만들어지면  그것의 segment를 위한 공간을 물리메모리에서 찾아야합니다. 프로세스마다 segment 수는 제각각이고 그 segment의 사이즈도 전부 다르기 때문입니다. - **Free list management algorithms**
* Extermal fragmentation 을 효율적으로 처리할 수 있어야합니다. 
    * memory compaction 작업을 수행합니다. 하지만 물리메모리의 정보들을 모두 copy 해주어야 하는데 이는 매우 큰 runtime overhead 를 발생시킬 수 있습니다. 따라서 memory compaction 은 메모리를 효율적으로 사용하는데에는 도움이 되지만 응용 프로그램이 실행하는 중에 상당한 overhead를 발생시킬 수 있습니다. 그러므로 선택적으로 수행하여야 합니다. 

Free list management algorithms
---
* 사용하지 않는 공간에 대해 리스트 형태로 관리합니다.
* 리스트마다 시작주소와 길이로 관리합니다. 
    * best fit 크기가 가장 비슷한 공간에 배정 
    * worst fit 가장 큰 공간에 배정 
        * 장단점이 모두 있습니다 best fit 의 경우 오히려 external fragmentation 을 더 발생시킬 수 있습니다. 둘다 sorting 에 대한 오버헤드가 존재합니다 
    * first fit : red block tree 에서 순서대로 탐색 후 사이즈가 맞다면 바로 할당해줍니다.
    * buddy algorithm 