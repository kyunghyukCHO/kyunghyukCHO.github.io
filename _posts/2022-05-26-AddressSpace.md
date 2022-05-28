---
layout: single
title:  " [운영체제] Address Spaces "
categories:
  - 운영체제
---

memory virtualization
===
운영체제는 물리적인 메모리에서 프로세스가 private 하고 large 한 Address space 를 가지고 실행될 수 있게 할 수 있어야 합니다. 한개의 프로세스가 memory 를 전부 독점하면 context switch overheads 등의 여러 문제가 발생하게 됩니다. 그러므로 여러개의 프로세스가 memory를 효율적으로 공유하도록 해야 합니다.    

* Address Space : 운영체제는 사용자에게 사용하기 쉬운 메모리 개념을 만들어야합니다. 이 개념이 주소 공간(address space)입니다. 말그대로 이는 개념이며 illusion 을 주는것과 같습니다. 주소 공간은 프로그램의 모든 메모리 상태를 갖고 있습니다. 코드, 스택, 힙과 같이 프로그램을 실행시키기 위한 모든 상태를 주소공간이 가지고 있습니다.

목표
---
* Transparency : 프로그램은 마치 고유한 private 물리적 메모리 공간을 가지고 있는 것처럼 행동합니다.
    * 운영체제가 제공한 환상이 응용 프로그램에게 가상화된다고 생각하게하면 안된다는 것 입니다.
* Efficiency : Time 과 memory space 는 효율적으로 운영되어야 합니다.
* Protection : 프로세스간 Address space 로 침범하지 않고 Protection 을 유지해야 합니다.

Type of Memory
---
* Stack : 함수의 호출, 지역변수를 할당할 때 컴파일러가 조작합니다. 
* Heap : 명시적으로 메모리할당을 할 때 Heap 에 저장됩니다. malloc 을 통해 할당된 메모리는 free 함수를 통해 반납해줘야 합니다.
* Common Errors
    * 할당된 메모리 초기화를 안함
    * 메모리의 free 를 안함
    * 메모리의 사용이 끝나기 전에 free 
    * 메모리를 반복적으로 free 
    * 올바르지 않은 free 

Address Translation
---
우리가 짜는 프로그램들은 전부 가상주소에 접근하게 되어 있습니다. 하지만 실제로 메모리에 접근하기 위해선 물리주소가 필요합니다. Address space 내에서 사용하는 가상주소를 물리주소로 변환하는 작업을 **운영체제** 가 해주게 됩니다. 기본적인 변환 도구들은 하드웨어가 제공하고 있지만, 그 도구의 사용은 운영체제가 담당합니다.      

* base register : 각 Address space 의 물리주소의 시작지점을 표시해 줍니다.
* bounds register : 각 Address space 의 물리주소의 끝지점을 표시해 줍니다.
* Physical address = Base + virtual address 
* Protection : memory reference 는 bounds 내에 있어야 합니다. 만약 Virtual address 가 bounds 보다 크다면 CPU 는 exception 을 발생시킵니다.

summary
---
이번 포스트에서는 가상 메모리라는 운영체제 구성 요소에대한 소개를 마쳤습니다. 하드웨어의 도움을 받아 운영체제는 가상 메모리 주소를 받아 물리 메모리 주소로 변환을 합니다. 이러한 작업을 통해 운영체제는 각 프로세스들과 운영체제를 보호하며 격리합니다. 우리는 다음 포스트부터 기초적인 필수 기법에 대한 설명을 시작으로 점차 고수준 정책을 배워 볼 것입니다.
