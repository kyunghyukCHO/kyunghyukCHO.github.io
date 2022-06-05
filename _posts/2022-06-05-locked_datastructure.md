---
layout: single
title:  " [운영체제] Lock-based concurrent Data Structures  "
---

Lock-based concurrent Data Structures
===
mutex Lock 으로 자료구조에 어떻게 lock 을 해줄것인지에 대한 논의 입니다. 여러 threads 가 동시에 접근하고 있는 공유 자료구조에서 이때 race condition 등의 문제가 발생할 수 있기 때문에 lock 을 이용합니다. 

how to add locks to data structure
---
* correctness : critical section 보장의 여부
* concurrency : 병렬성을 어떻게 최대한 보장해줄 수 있는가의 여부

Counter
===

concurrent counter
---
동작은 할 수 있으나 여러 threads 가 counter 에 접근하려고 할 때, 성능적으로 좋은 방법은 아님

scalable counter (sloppy counter)
---
* local counter per cpu core
* global counter
* locks
local counter 에 대해 증감할 때 다른 thread와 관련이 없습니다. 주기적으로 한시적인 local counter value 를 global counter value 에 반영합니다. 각각의 counter 에 접근하기 위한 lock 들이 구현되어 있습니다. 여전히 lock 은 존재하지만 병렬적인 여러 스레드들의 사이에서 lock 에 대한 경쟁을 줄여 성능을 향상시킬 수 있습니다. 

Linked Lists
===
Concurrent Linked Lists
---
메모리 할당 부분을 lock 합니다. 동작은 할 수 있으나 마찬가지로 여러 스레드가 접근하려고 할 때, critical section 을 너무 크게 정의했기 때문에 병렬성이 떨어집니다. 즉, 성능적으로 좋은 방법은 아닙니다.

Scaling Linked Lists
---
메모리를 할당하는 부분이 아닌 Linked List 의 링크를 연결하는 부분을 lock으로 감싸게 됩니다. 특정 key를 갖고 있는 node 를 탐색하는 lookup 함수에서 해당 node 발견시 break 을 걸어 탈출하고 안전하게 unlock 합니다. 이는 unlock 되지 않고 return 되는 것을 방지하기 위함 입니다.

concurrent Queues
===
* Enqueue에서 Tail을 연결할 때 TailLock 호출 
* Dequeue에서 Head를 연결할 때 headLock 호출

