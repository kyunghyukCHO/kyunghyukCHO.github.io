---
layout: single
title:  " [운영체제] CPU Scheduling "
---

CPU Scheduling
===
Time sharing 에서 Timer Interrupt 가 발생했을 때, 어떤 알고리즘에 의하여 context switch 를 할것인가에 대한 policy 를 알아보고자 한다. 

Turnaround Time
---
스케쥴링 알고리즘의 비교를 하기 위한 일종의 **'잣대'** 입니다.   
**turnaround time = completion time - arrival time**

First in, First out (FIFO)
---
Average turnaround time = (10 + 20 + 30) / 3 = 20    
FIFO 알고리즘은 긴 시간을 요구하는 프로세스가 앞에 위치할 때 Turnaround time 이 크게 늘어나면서 성능이 나빠집니다. 이러한 FIFO 알고리즘을 해결하기위한 알고리즘은 **SJF** 스케쥴링 알고리즘 입니다.     
Average turnaround time = (100 + 110 + 120) / 3 = 110    

Shortest Job First (SJF)
---
실행시간이 짧은 프로세스부터 먼저 실행을 시킵니다. 모든 job 들이 같은 시간에 도착한다고 가정할 때 최적합니다. 하지만 이는 비현실적인 가정이므로 사실상 좋은 성능이라고 말하기 어렵습니다. 예를들어 A,B,C 가 같은 시간에 도착하는 것이 아닌, A가 도착하고 B,C 가 10에 도착한다면 Avg turnaround time 이 상승하며 성능이 나빠지게 됩니다.     
Average turnaround time = (10 + 20 + 120) / 3 = 50    

**다음 스케쥴러 알고리즘에 대해 말하기 전에, Non-preemptive scheduler 와 Preemptive scheduler 에 대해 말하고자 합니다.**

Non-preemptive scheduler
---
non선점 스케쥴러에서는 일단 프로세스가 실행이 되면, 다음 프로세스의 스케쥴링은 실행되고 있는 프로세스가 끝난 후에 스케쥴링 알고리즘이 동작합니다. 

Preemptive scheduler
---
실행하고 있던 프로세스를 멈추게 하고 CPU 자원을 다른 프로세스에게 주어 다른 프로세스가 실행할 수 있도록 **context switch**가 반복적으로 발생하는 스케쥴러 입니다. 현재 운영체제에는 Preemptive scheduler 를 사용하고 있습니다. 

Shortest Time-to-Completion First (STCF)
---
**=** Preemptive Shortest Job First (PSJF)
앞서 본 SJF 를 Preemptive 하게 바꾼 스케쥴러 입니다.

**스케쥴링의 성능을 비교하기 위한 잣대로 Turnaround time 이외에 Response time 이라는 것이 존재합니다.**

Response time
---
response time = firstrun time - arrival time      

STCF 알고리즘에서 turnaround time 이 좋을지라도, 해당 프로세스들의 도착시간과 첫 runtime 의 관계에 따라 response time 은 나쁠 수 있습니다. 이러한 STCF 의 문제점을 해결하기 위해 제안된 방법은 **Round-Robin** 알고리즘 입니다.    

Round-Robin (RR)
---
시간을 Time slicing 하여 매우 작은 time slice 마다 스케쥴링 알고리즘을 실행시킵니다. 이 스케줄링 알고리즘은 Average Response time 은 좋을 수 있으나, turnaround time 은 다소 안좋게 나옵니다. 왜냐하면 time slice 로 모두 쪼개지므로 모든 프로세스가 긴 arrival time 에 비슷하게 끝나게 되기 때문입니다. 하지면 현재 운영체제는 모두 round-robin 방식의 변형된 형태를 사용합니다. 응용 프로그램들이 사용자들과 I/O device 와 interrupt가 훨씬 많고 이는 round-robin 이 일반적으로 성능을 좋게 제공하기 때문입니다. 여기서 관건은 **time slice 를 어떤 값으로 설정하는가** 입니다. 

**모든 Job 들은 오직 CPU 만을 사용하지 않습니다. 앞서 CPU 자원을 어떤 프로세스가 얼만큼 사용하는지에 대해 논의했다면, 뒷 내용에서 job들이 CPU 자원이 아닌 다른 자원들을 어떻게 사용하는지에 대해 논의해보고자 합니다.**

Incorporating I/O
---
한 프로세스가 CPU를 한 time slice 만큼 점유했다고 가정했을 떄, 프로세스가 I/O 작업 등으로 running 에서 blocked 상태로 가면서 DISK 자원을 점유하고 CPU 자원을 포기할 때가 생깁니다. 이때, 스케쥴러가 불러지고 낭비되고 있는 CPU에 다른 프로세스를 실행시킵니다.     
**즉, 놀고있는 CPU 자원을 다른 프로세스에게 할당해주며 CPU가 놀지 않게끔 스케쥴링 해줍니다.**



