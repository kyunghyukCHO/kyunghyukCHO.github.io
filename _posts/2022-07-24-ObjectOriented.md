---
layout: single
title:  " [SPRING] 스프링 & 객체 지향 설계"
---

스프링(SPRING)이란?
---
과거에는 EJB를 사용하여 웹 애플리케이션을 개발하였습니다. 하지만 EJB에 의존적인 개발은 객체지향의 큰 장점들을 다 잃어버렸고, 이러한 단점들을 탈피하여 순수한 객체지향인 JAVA 기반의 프레임워크를 사용할 수 있게 한것이 스프링의 핵심이자 시작입니다. 스프링에는 여러가지 프로젝트 기술들이 있습니다. 그 중 핵심이 되는 것은 스프링 부트와 스프링 프레임워크가 있습니다. 스프링 부트는 스프링의 각종 프로젝트들과 스프링 프레임워크들을 편리하게 사용할 수 있는 기능들을 제공하는 기술입니다.      
* 스프링 CORE
    * JAVA 기반의 프레임워크
    * 객체 지향 언어가 가진 강력한 특성을 살려내는 프레임워크
    * 객체 지향 애플리케이션 개발 지원

스프링 부트(SPRING BOOT)
---
스프링을 편리하게 사용할 수 있도로 지원하며, 최근에는 기본으로 사용되고 있습니다.     
* 단독 실행 스프링 애플리케이션을 쉽게 생성함
* 웹 서버를 내장하여 별도의 웹 서버 설치가 필요하지 않음
* 스프링과 외부 라이브러리 자동 구성
* 메트릭, 상태 확인, 외부 구성 같은 프로덕션 준비 기능 제공

객체 지향 프로그래밍
---
객체 지향 프로그래밍이란 절차 지향에서 벗어나 여러개의 독립된 "객체" 단위들의 모임으로 파악하고자 하는 방법입니다. 각각의 객체는 메세지를 주고받고, 데이터를 처리합니다. 객체 지향 프로그래밍은 "객체"를 이용하기 때문에 프로그램을 유연하고 변경이 용이하게 만드는 장점이 있습니다. 즉, 컴포넌트를 쉽고 유연하게 변경하면서 개발하는 방법입니다.

다형성 (Polymorpism)
---
프로그램 언어의 다형성(多形性, polymorphism; 폴리모피즘)은 그 프로그래밍 언어의 자료형 체계의 성질을 나타내는 것으로, 프로그램 언어의 각 요소들(상수, 변수, 식, 오브젝트, 함수, 메소드 등)이 다양한 자료형(type)에 속하는 것이 허가되는 성질을 가리킨다. 반댓말은 단형성(monomorphism)으로, 프로그램 언어의 각 요소가 한가지 형태만 가지는 성질을 가리킨다.       
[위키백과](https://ko.wikipedia.org/wiki/%EB%8B%A4%ED%98%95%EC%84%B1_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)). 
과거에는 EJB를 사용하여 웹 애플리케이션을 개발하였습니다. 하지만 EJB에 의존적인 개발은 객체지향의 큰 장점들을 다 잃어버렸고, 이러한 단점들을 탈피하여 순수한 객체지향인 JAVA 기반의 프레임워크를 사용할 수 있게 한것이 스프링의 핵심이자 시작입니다. 스프링에는 여러가지 프로젝트 기술들이 있습니다. 그 중 핵심이 되는 것은 스프링 부트와 스프링 프레임워크가 있습니다. 스프링 부트는 스프링의 각종 프로젝트들과 스프링 프레임워크들을 편리하게 사용할 수 있는 기능들을 제공하는 기술입니다.           

즉, 하나의 부모타입 참조변수가 여러 자식 타입의 인스턴스를 가질 수 있는것을 말합니다. 다형성은 역할과 구현으로 분리하는 개념입니다. 다형성을 실세계에 비유하면 운전차-자동차 , 공연 무대 그리고 키보드 마우스 같은 세상의 표준 인터페이스들이 있습니다. 첫번째 예시를 설명하면 자동차라는 인터페이스 모델이 존재한다면, 그 인터페이스의 공통 기능들을 갖는 여러가지 자동차 모델들이 나올 수 있습니다. 또한, 운전자(클라이언트)는 자동차의 인터페이스 모델에 대해서만 알면 된다는 장점이 있습니다.     

* 역할과 구현을 분리
    * 클라이언트는 대상의 역할(인터페이스)만 알면 됩니다.
    * 클라이언트는 구현 대상의 내부 구조를 몰라도 됩니다.
    * 클라이언트는 구현 대상의 내부 구조가 변경되어도 영향을 받지 않습니다.
    * 클라이언트는 구현 대상 자체를 변경해도 영향을 받지 않습니다.
* JAVA 의 다형성
    * 역할 = 인터페이스
    * 구현 = 인터페이스를 구현한 클래스, 구현 객체 
    * 객체 설계시 역할(인터페이스)를 먼저 부여하고, 그 역할을 수행하는 구현 객체를 만듭니다.
* 객체의 협력
    * 클라이언트 : 요청 , 서버 : 응답
    * 수 많은 객체 클라이언트와 객체 서버는 서로 협력 관계를 갖습니다. 
* 다형성의 본질
    * 인터페이스를 구현한 객체 인터페이스를 실행 시점에 유연하게 변경할 수 있습니다.
    * 클라이언트를 변경하지 않고, 서버의 구현 기능을 유연하게 변경할 수 있습니다. 
* 역할과 구현의 분리
    * 유연하고 변경이 용이합니다
    * 설계의 확장이 가능합니다
    * 클라이언트에게 영향을 주지 않는 변경이 가능합니다
    * 인터페이스를 안정적으로 잘 설계하는 것이 중요합니다
* 예시 (스프링 입문)
    * MemberRepository : 인터페이스(역할)
    * MemoryRepository : 구현
    * JdbcRepository : 구현
    * JpaMemberRepository : 구현 

좋은 객체 지향의 5가지 원칙(SOLID)
---
클린코드로 유명한 로버트 마틴이 좋은 객체 지향 설계의 5가지 원칙을 정리하였습니다.     
* SRP : 단일 책임 원칙 (Single responsibility principle)
    * 한 클래스는 하나의 책임만 가져야 한다.
    * 중요한 기준은 변경 - 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것
* OCP : 개방-폐쇄 원칙 (Open/closed principle)
    * 소프트웨어 요소는 확장에는 열려 있느나 변경에는 닫혀 있어야 한다
    * 다형성 활용
    * 인터페이스를 구현한 새로운 클래스를 하나 만들어서 새로운 기능을 구현 
    * 문제점
        * 클라이언트가 구현 클래스를 직접 선택
        * 구현 객체를 변경하려면 클라이언트 코드를 변경해야함
        * 분명 다형성을 사용했지만 OCP 원칙을 지킬 수 없음
* LSP : 리스코프 치환 원칙 (Liskov substitution principle)
    * 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다
    * 하위 클래스는 인터페이스 규약을 다 지켜야 한다.
    * 단순히 컴파일에 성공하는 것을 넘어서는 규약을 지킨다는 원칙 
* ISP : 인터페이스 분리 원칙 (Interface segregation principle)
    * 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다
    * 예시
        * 자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리
        * 사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리 가능
        * 정비 인터페이스는 운전자 클라이언트에 영향을 주지 않음
    * 인터페이스가 명확해지고, 대체 가능성이 높아진다
* DIP : 의존관계 역전 원칙 (Dependency inversion principle)
    * 프로그래머는 “추상화에 의존해야지, 구체화에 의존하면 안된다.” 의존성 주입은 이 원칙 을 따르는 방법 중 하나다.
    * 쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻
    * 객체 세상도 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다. 
    * 구현체에 의존할 시 변경이 매우 어려워짐
    * 문제점
        * 인터페이스에 의존하지만, 구현 클래스도 동시에 의존하는 경우가 발생
            * MemberService 클라이언트가 구현 클래스를 직접 선택
            * MemberRepository m = new MemoryMemberRepository();
        * DIP 위반

결론
---
스프링은 DI(의존성 주입), DI 컨테이너 제공과 같은 기술로 다형성 + OCP, DIP 를 가능하게 지원합니다. 즉, 클라이언트 코드의 변경 없이 기능 확장이 가능하며 쉽게 부품을 교체하듯 개발이 가능해집니다. 
