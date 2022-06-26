---
layout: single
title:  " [JAVA] 인터페이스(Interface) "
---

인터페이스(interface)란?
---
자식 클래스가 여러 부모 클래스를 상속받을 수 있다면, 다양한 동작을 수행할 수 있다는 장점을 가지게 될 것입니다. 하지만 클래스를 이용하여 다중 상속을 할 경우 메소드 출처의 모호성 등 여러 가지 문제가 발생할 수 있어 자바에서는 클래스를 통한 다중 상속은 지원하지 않습니다. 하지만 다중 상속의 이점을 버릴 수는 없기에 자바에서는 인터페이스라는 것을 통해 다중 상속을 지원하고 있습니다. 인터페이스(interface)란 다른 클래스를 작성할 때 기본이 되는 틀을 제공하면서, 다른 클래스 사이의 중간 매개 역할까지 담당하는 일종의 추상 클래스를 의미합니다. 자바에서 추상 클래스는 추상 메소드뿐만 아니라 생성자, 필드, 일반 메소드도 포함할 수 있습니다. 하지만 인터페이스(interface)는 오로지 추상 메소드와 상수만을 포함할 수 있습니다.

인터페이스 선언
---
```java
접근제어자 interface 인터페이스이름 {
    public static final 타입 상수이름 = 값;
    ...
    public abstract 메소드이름(매개변수목록);
    ...
}
```        
단, 클래스와는 달리 인터페이스의 모든 필드는 public static final이어야 하며, 모든 메소드는 public abstract이어야 합니다. 이 부분은 모든 인터페이스에 공통으로 적용되는 부분이므로 이 제어자는 생략할 수 있습니다. 이렇게 생략된 제어자는 컴파일 시 자바 컴파일러가 자동으로 추가해 줍니다.

인터페이스 구현
---
```java
class 클래스이름 implements 인터페이스이름 { ... }
```    
만약 모든 추상 메소드를 구현하지 않는다면, abstract 키워드를 사용하여 추상 클래스로 선언해야 합니다.

예제
---
```java
interface Animal { public abstract void cry(); }
class Cat implements Animal {
    public void cry() {
        System.out.println("냐옹냐옹!");
    }
}
class Dog implements Animal {
    public void cry() {
        System.out.println("멍멍!");
    }
}
public class Polymorphism03 {
    public static void main(String[] args) {
        Cat c = new Cat();
        Dog d = new Dog();
        c.cry();
        d.cry();
    }
}
```

다중상속 예제
---
```java
interface Animal { public abstract void cry(); }
interface Pet { public abstract void play(); }
class Cat implements Animal, Pet {
    public void cry() {
        System.out.println("냐옹냐옹!");
    }
    public void play() {
        System.out.println("쥐 잡기 놀이하자~!");
    }
}
class Dog implements Animal, Pet {
    public void cry() {
        System.out.println("멍멍!");
    }
    public void play() {
        System.out.println("산책가자~!");
    }
}
public class Polymorphism04 {
    public static void main(String[] args) {
        Cat c = new Cat();
        Dog d = new Dog();
        c.cry();
        c.play();
        d.cry();
        d.play();
    }
}
```

