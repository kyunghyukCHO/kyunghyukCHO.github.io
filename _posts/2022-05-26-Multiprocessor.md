---
layout: single
title:  " [운영체제] Multiprocessor Scheduling "
---

Multiprocessor Scheduling
===
CPU 즉, CORE 가 여러개 있다고 가정했을 때, multiprocessor scheduling 에 대해 알아보고자 합니다.

Single-queue multiprocessor scheduling (SQMS)
---
A,B,C,D and E 프로세스가 있고, 1개의 ready Queue, 그리고 4개의 CPU가 있다고 가정했을 때, ready Queue 에서 4개의 CPU가 프로세스들을 연속하여 가져옵니다. 이 때, 예를들어 A 프로세스는 CPU 0 부터 CPU 3 까지 이동하며 각 CPU 에 저장된 캐시메모리를 쓰지 못하게 됩니다.        

**단점**    
즉, 메모리 친화도가 매우 떨어지게 됩니다. 또한 각각의 CPU 의 동기화가 필요하므로 scalability 가 나빠집니다.

Multi-Queue Scheduling
---
* CPU 마다 ready Queue 가 한개씩 주어집니다.
* 한개의 프로세스는 하나의 ready Queue 에만 존재합니다. -> job이 들어왔을 때, Random 혹은 제일 짧은 queue 에 배정됩니다.
* 각각의 CPU가 독립적으로 자기만의 ready Queue에 접근해서 스케줄링하게 됩니다.

**장점**     
메모리 친화도가 좋아지며, 동기화가 따로 필요하지 않는다는 장점이 있습니다.    

Multi-Queue Scheduling - load imbalance
---
프로세스의 총 실행시간 혹은 각각의 Queue 의 프로세스의 수에 따라 CPU 자원이 imbalance 하게 낭비되거나 활용되지 못하는 문제가 있습니다. 이를 해결하기 위해 job을 중간중간 **Migration** 해주어야 합니다.

Work Stealing
---
* 한가한 CPU 스케쥴러가 다른 Queue 의 job 을 가져옵니다. 
* 단, memory bound 하지 않다고 판단되는 job 들을 우선적으로 가져옵니다.

---------------------------------

Linux CPU Schedulers
===
* Completely Fair Scheduler (CFS)
* Real-Time Schedulers
* Deadline Scheduler

Completely Fair Scheduler (CFS)
---
각각이 task 들은 virtual runtime 에 기반하여 red-black tree 구조로 저장됩니다. 여기서 vruntime 은 현재까지 (실행시간 + (최근에 CPU 를 사용한 시간 * weight 값)) 이 됩니다. nice value 라는 것은 얼만큼 task가 cpu 를 양보를 잘 하는지에 대한 척도인데, 이 nice value (-20 ~ 19) 값에 따라 weight 값이 정해져 있습니다.     

**(주의) priority 와 nice 는 다른 개념입니다. 높은 priority 는 preemptive 하지만 nice value 는 non-preemptive 합니다.**    

* Priority = nice + 120
    * CFS : 100 ~ 139 -> 100에 가까운 수일때 CPU 자원을 더 주게 되므로, 더 높은 우선순위를 갖습니다.
    * 0~99 는 real-time schedulers 에 의해 예약되어 있습니다. 
    * renice command : root 를 통해 user 는 task 의 nice value 값을 수정할 수 있습니다.

* load imbalance 
    * Load of thread : average CPU utilization ( weighted by the thread's priority)
    * Load of core : sum of the loads of the threads
    * Tries to even out the load of cores






