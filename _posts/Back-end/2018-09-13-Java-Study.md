---
layout: post
title: "Java Study"
categories: Back-end
author : choising
tags: java string
---

## Java

### Java 실행과정

- 클래스와 인스턴스의 차이를 찾아보다가, 메모리 구조가 궁금했고 자바 실행 과정이 궁금했다.

![java](https://github.com/Oraindrop/oraindrop.github.io/blob/master/assets/_img/javaruntime.png?raw=true)

- 클래스 파일
    - java 파일 컴파일하여 JVM에 이해할 수 있는 바이트 코드로 변환된 소스 코드.

- JVM
    - java byte code 실행 머신
    - 운영체제 위에서 동작하기에 java가 운영체제 독립적
    - 구성은 아래와 같다.
    - 자바 인터프리터
        - 자바 바이트 코드를 읽고 해석
    - 클래스 로더
        - 동적으로 클래스를 로딩
        - 클래스 로딩 : 실행한 클래스 파일을 읽고 메모리에 올려놓는 과정.        
    - JIT 컴파일러
        - 바이트 코드를 런타임에 실제 기계어로 변환
        - 동적 번역, 실행속도 향상
    - 가비지 컬렉터
        - 사용하지 않는 메모리 자동 회수

- Runtime data area

    1. Method area
        - 클래스 로더에 의해서 로딩된 클래스가 저장.
        - 클래스 메타정보(메소드, 변수)
        
    2. Stack area
        - 메소드 정보가 저장.
        - 실행이 끝나면 정보 삭제.

    3. Heap area
        - 인스턴스가 위치.

    4. Register area
        - JVM이 수행할 명령어의 주소가 저장

    5. Native method area
        - OS 정보, 리소스를 사용하거나 접근하기 위함

### java coding convention

- 클래스 구현순서

```java
class A {
    상수(static final) 또는 클래스 변수

    인스턴스 변수

    생성자

    메소드
}
```

### 용어

- why constant use `static final`
    - final : 변경 불가.
    - static : 모든 인스턴스 공유
        - static이 없다면 각 인스턴트마다 상수가 새로 생성되기 때문에 메모리 낭비.

- 패키지(package)
    - 관련된 클래스들을 모아 관리할 수 있도록 한다.
        - 배포 시 관련 코드들을 묶어 배포하기 용이하다.
    - 만약, 패키지를 사용하지 않고 프로그램이 커진다면 수백, 수천 개의 클래스를 찾는 수고를 해야할 것이다.
    - 모든 패키지 이름은 소문자를 사용하는 것이 관례이다.
    - 패키지로 구분하면 동일한 클래스 이름을 사용할 수 있다.

- Production code
    - 프로그램이 구현된, 실제 로직을 담당하는 코드.

- Test code
    - 프로덕션 코드(production code)가 정상적으로 동작하는지 확인하는 코드.
    - junit을 통한 테스트 시 @Test는 test하려는 메소드와 동일한 이름을 지니는 것이 권장된다.

### import static
- static으로 import 시
- Class 이름 없이 접근할 수 있다.
- static method import.

```java
    static import java.util.System.*
    out.println("hello world");
```

>**imports** classes from packages
>allowing them to be used without package qualification
>the **static import** declaration imports static members from classes 
>allowing them to be used without class qualification.

>**Very sparingly**
>when you require frequent access to static members from one or two classes. 

- package에 포함된 class를 import할 경우, 해당 클래스 사용 시 패키지 인증(패키지를 입력)할 필요가 없다.
- static import의 경우, 클래스를 인증(입력)할 필요가 없다.
- 매우 조심히 사용할 것.
- 빈번하게 멤버를 사용해야 할 때만.

### 생성자(Constructor)

- 클래스의 이름과 같은 메소드이며 리턴값이 없다(void를 사용하지는 않는다.)

- 인스턴스가 생성될 때 호출되는 '인스턴스 초기화 메소드'
    - 인스턴스 변수의 초기화 작업에 주로 사용.
    - 인스턴스 생성 시에 실행되어야 할 작업을 위해서 사용.

- 오해하지 말 것.
    - 연산자 new가 인스턴스를 생성하는 것이지 생성자가 인스턴스를 생성하는 것은 아니다.
    - 인스턴스변수들의 초기화에 사용되는 조금 특별한 메소드일 뿐.

- 모든 클래스에는 반드시 하나 이상의 생성자가 존재.
    - 정의되어 있는 생성자가 하나도 없을 경우에만, 컴파일러가 argument도 없고 아무런 내용도 없는 생성자를 추가한다.

### String

- String List to String Array
    - `String[] strings = list.stream().toArray(String[]::new);`

- String vs StringBuilder vs StringBuffer
    - String
        - immutable(불변)
        - String + String 시 사실 기존 문자열에 붙이는 것이 아니라
        - 새로운 String 객체를 만든 후, 연결된 문자열을 저장하고 그 객체를 참조하도록 하는 것.
        - 메모리 할당과 해제가 발생하므로, 복잡한 경우 효율이 떨어진다.

    - StringBuilder , StringBuffer
        - mutable(가변)
        - 문자열 연산 등으로 기존 객체의 공간이 부족한 경우, 기존 버퍼 크기를 늘리며 유연하게 동작.
        - StringBuilder와 StringBuffer가 제공하는 메소드는 동일.
        - 차이점은 동기화 여부.
            - StringBuffer : 각 메소드 별 Synchronized Keyword가 존재. 멀티스레드 환경에서 동기화를 지원.
            - StringBuilder : 동기화를 보장하지 않음.
        - 즉, 단일 스레드 환경에서는 StringBuilder를 멀티 스레드 환경에서는 StringBuffer를 사용하면 될 듯.

### 접근제어자(modifier)

modifier | 같은 클래스 | 같은 패키지 | 자손 클래스 | 전체
---|:---|:---|:---|:---
public | O | O | O | O
protected | O | O | O | X
default(or package) | O | O | X | X
private | O | X | X | X

priavte - default(or package) - protected - public 순으로 보다 많은 접근을 허용한다.

대상 | 사용 가능한 modifier 
---|:---
클래스 | public, (default)
메소드<br>멤버필드| public, protected, (dafault), private
지역변수 | -
