---
layout: single
title:  " [자료구조] Tree Traversal and Parsing "
---

Tree Traversal
===
트리구조에는 단순히 number 가 아닌 구조를 가진 value 들도 들어갈 수 있습니다. 예를들어 number 만 들어갔을 경우 parsing 이 문제되지 않지만 연산자를 포함한 식들이 들어가게 된다면 parsing 에 대한 issue 가 생기게 됩니다. 여기서 Traversal 은 전체 구조를 파악하여 의미있는 일을 하기 위함입니다.

Traversal
---
* Traversal 은 모든 node 들을 어떤 order 로 visit 하는 것을 의미합니다.
* Visit 은 어느 node 에 "있다" 라는 의미는 아닙니다.
* Visit 은 어느 node 에서 어떤 작업을 한다는 의미입니다.
* Order
    * PreOrder
    * InOrder
    * PostOrder

Parsing
===

Notation
---
* prefix Notation : *+/635-64
* infix Notation : (6/3+5)*(6-4)
* postfix Notation : 63/5+64-* 

Parsing (for Code Generation)
---
parsing 은 컴파일러에게 매우 중요합니다. fomula 를 만드는 tree 를 구축하는 process 를 parsing 이라고 부릅니다.

The Parsing Algorithm (No Parentheses)
---
완벽한 증명은 다루지 않도록 하겠습니다. (too complicated..)        
* 가정
    * 연산자들은 우선순위가 있습니다
    * 만약 우선순위가 같다면 왼쪽 연산자의 우선순위가 높다고 가정합니다.

* 식을 왼쪽부터 오른쪽으로 읽습니다
    * 피연산자면 노드를 만들고 두번째 스택에 넣습니다
    * 연산자면 첫번째 스택의 탑과 우선순위 비교
        * 입력값이 우선순위가 높으면 스택에 넣습니다
        * 스택의 top이 더 높거나 같으면 
            * 두번째 스택에서 피연산자를 두개 꺼내고
            * 결과물을 두번째 스택에 다시 넣습니다   
            * 반복
        * $ 와 $ 가 만나면 finish : $ 는 Low priority

The Parsing Algorithm (With Parentheses)
---
* Parentheses have two priority
    * 여는 괄호가 input에 있을 때는 가장 높은 우선순위를 갖습니다.
    * 여는 괄호가 스택에 있을 때는 가장 낮은 우선순위 입니다.
    * 닫는 괄호는 가장 낮은 우선순위를 가지며 스택에 들어가지 않습니다.
    * 닫는 괄호에셔 연산이 되고 여는 괄호를 만나면 사라집니다.

How to generate Code
---
Parse Tree 를 Traversal (postOrder) 하면서 연산자 일므의 임시 노드를 만들어 instruction 을 반복적으로 수행합니다.





