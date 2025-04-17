---
tags: 
 - pksoooooo
 - github
title: Object
description: Object Class
---

# 자바 중급 1편 - Object 클래스

---

### `java.lang` 패키지 소개
- 자바의 기본 클래스들이 포함된 패키지
- **자동 import** 되어 있어 별도로 import 하지 않아도 됨

#### 주요 클래스들
- `Object`: 최상위 부모 클래스
- `String`: 문자열 클래스
- `Integer`, `Long`, `Double`: 래퍼 클래스, 기본형 데이터 타입을 객체로 만든 것
- `Class`: 클래스 정보 제공
- `System`: 시스템 기능 제공

---

### Object 클래스

#### 기본 개념
- 모든 클래스는 `Object`를 상속 받음 (명시하지 않아도 자동 적용)
- `extends Object`는 생략 권장

```java
public class Parent { }
// 위 코드는 다음과 같다
public class Parent extends Object { }
```

```java
public class Child extends Parent {
    public void childMethod() {
        System.out.println("Child.childMethod");
    }
}
```

#### 예제: Object 메서드 호출
```java
Child child = new Child();
child.toString(); // Object 메서드 호출
```

#### Object 클래스가 필요한 이유
- **공통 기능 제공**
  - `toString()` : 객체 정보 출력
  - `equals()` : 객체 비교
  - `getClass()`: 클래스 정보
- **다형성 지원**: 모든 객체를 `Object` 타입으로 처리 가능

---

### Object 다형성

```java
class Dog {
    public void sound() { System.out.println("멍멍"); }
}

class Car {
    public void move() { System.out.println("자동차 이동"); }
}

public class ObjectPolyExample1 {
    public static void main(String[] args) {
        Dog dog = new Dog();
        Car car = new Car();
        action(dog);
        action(car);
    }

    private static void action(Object obj) {
        if (obj instanceof Dog dog) dog.sound();
        else if (obj instanceof Car car) car.move();
    }
}
```

#### 장점
- 다양한 객체를 `Object` 하나로 받아 처리 가능

#### 한계
- `Object`는 `sound()`나 `move()` 메서드를 알지 못하므로 **다운캐스팅** 필요

---

### Object 배열
```java
Object[] arr = {new Dog(), new Car(), new Object()};
System.out.println("전달된 객체 수: " + arr.length);
```
- 모든 객체를 담을 수 있는 범용 배열 생성 가능

---

### toString()

#### 기본 동작
```java
Object obj = new Object();
System.out.println(obj.toString());
System.out.println(obj);
```
- 결과: `java.lang.Object@주소`
- 내부 구현:
```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

#### 오버라이딩 예제
```java
class Dog {
    private String name;
    private int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{name='" + name + "', age=" + age + "}";
    }
}
```

#### 다형성과 toString
```java
public static void print(Object obj) {
    System.out.println("객체 정보 출력: " + obj.toString());
}
```
- `System.out.println()`은 내부적으로 `toString()` 호출

---

### Object와 OCP (개방-폐쇄 원칙)

#### OCP란?
- **OCP (Open-Closed Principle)**: 소프트웨어 구성 요소는 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다.
- 즉, 기존 코드를 변경하지 않고 기능을 추가할 수 있도록 설계해야 함

#### 나쁜 예: BadObjectPrinter (구체적인 클래스에 의존)
```java
public class BadObjectPrinter {
    public void print(Car car) {
        System.out.println("객체 정보 출력: " + car.carInfo());
    }

    public void print(Dog dog) {
        System.out.println("객체 정보 출력: " + dog.dogInfo());
    }
}
```
- `Car`, `Dog` 클래스가 추가될 때마다 새로운 `print()` 메서드를 계속 추가해야 함
- **변경에 닫혀 있지 못함 → OCP 위반**

#### 좋은 예: ObjectPrinter (추상화에 의존)
```java
public class ObjectPrinter {
    public void print(Object obj) {
        System.out.println("객체 정보 출력: " + obj.toString());
    }
}
```
- 모든 클래스가 상속받는 `Object` 타입을 사용
- 자식 클래스에서 `toString()` 을 오버라이딩하면, `ObjectPrinter` 는 그대로 사용 가능
- **확장에 열려 있고 변경에 닫혀 있음 → OCP 만족**

#### 구조 분석
- `ObjectPrinter` 는 `Object` 에만 의존하며, `Car`, `Dog` 등 구체적인 클래스에 의존하지 않음
- 다형성 + 메서드 오버라이딩을 활용함으로써 새로운 클래스를 추가해도 기존 코드는 변경하지 않아도 됨

#### 핵심 요약
| 항목 | BadObjectPrinter | ObjectPrinter |
|------|------------------|----------------|
| OCP 위반 여부 | ❌ 위반 | ✅ 준수 |
| 의존 대상 | 구체적인 클래스 | 추상적인 `Object` |
| 기능 확장 시 | 코드 수정 필요 | 코드 수정 없이 확장 가능 |
| 설계 유연성 | 낮음 | 높음 |

#### OCP가 잘 지켜지면?
- 새로운 기능이나 객체가 추가되어도 기존 코드를 건드릴 필요 없음
- 유지보수가 쉬워지고 오류 가능성이 줄어듦

---

### equals() - 동일성과 동등성

#### 기본 비교
```java
User u1 = new User("id-100");
User u2 = new User("id-100");
System.out.println(u1 == u2);         // false
System.out.println(u1.equals(u2));    // 기본은 false
```
- `==` : 참조 비교 (동일성)
- `equals()` : 기본은 동일성 비교 (재정의 전까지)

#### equals() 재정의 예시
```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (obj == null || getClass() != obj.getClass()) return false;
    User user = (User) obj;
    return Objects.equals(id, user.id);
}
```

#### equals() 규칙
1. 반사성
2. 대칭성
3. 추이성
4. 일관성
5. null 비교 시 false 반환

---

### 실습 예제: Rectangle 클래스 구현

```java
public class Rectangle {
    private int width;
    private int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public String toString() {
        return "Rectangle{" +
                "width=" + width +
                ", height=" + height +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Rectangle that = (Rectangle) o;
        return width == that.width && height == that.height;
    }
}
```

#### 실행 결과
```
Rectangle{width=100, height=20}
Rectangle{width=100, height=20}
false
true
```

---

### Object 클래스의 기타 메서드 (요약)
| 메서드 | 설명 |
|--------|------|
| `clone()` | 객체 복사 (잘 사용하지 않음) |
| `hashCode()` | 해시 기반 자료구조에 사용 (equals와 함께) |
| `getClass()` | 클래스 정보 반환 |
| `notify()`, `wait()` | 멀티스레드 동기화 용도 |

---

### 마무리
- `Object` 클래스는 자바 객체지향의 핵심 기반
- 다형성, 공통 기능 제공, OCP 실현 등에 중심적인 역할
- `toString()` 과 `equals()` 는 가장 많이 오버라이딩하는 메서드
- 자바 언어 자체가 객체지향 원칙을 잘 반영하고 있음

