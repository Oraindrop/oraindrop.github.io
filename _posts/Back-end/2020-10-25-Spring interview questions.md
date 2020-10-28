---
layout: post
title:  "Spring Interview Questions and Answers (작성중)"
categories: Back-end
author : choising
tags: spring, java
---

## 1. @Bean vs @Component

- @Bean 은 Method level, @Component 는 Class level (ElementType.Type)
    - 이게 핵심인 것 같음 이게 중요한게, 그래서 3rd-party lib 같은 경우 클래스에 직접적으로 @Component 를 달 수가 없으니 팩토리클래스 같은걸 만들어서 메소드에 @Bean 어노테이션을 달고 객체를 return 하도록한다.
    - 이 이야기에서 알 수 있듯 @Bean 은 인스턴스 생성로직을 제어할 수 있다. 메소드를 구현할 수 있으니까.
- @Bean 은 @Configuration 어노테이션이 필요하다. @Component 는 노필요.
- @Component 는 Specializtion 이 있음, @Controller, @Service 등 @Bean 에는 없음
- @Component does not decouple the declaration of the bean from the class definition where as @Bean decouples the declaration of the bean from the class definition.
    - 이해가 잘 안되는데, @Component 는 클래스에 하나고, @Bean 은 메소드당 있으니 클래스와 Bean 의 선언이 분리되었다고 하는거려나.


## 2. Tight Coupling vs Loose Coupling

- A class 내부에서 다른 B class 또는 C Interface 를 이용할 때 A 가 B/C 에 의존한다, 디펜던시가 있다 라고 말한다.
    - 이 때 A-B/C 간 커플링이 형성

- Tight Coupling = 단단한 결합, 긴밀한 결합, 밀접한 결합 
    ```
    class Car {

        private HyundaiEngine engine;

        public Car(HyundaiEngine hyundaiEngine) {
            this.engine = hyundaiEngine;
        }

    }
    ```
    - Car 인스턴스 생성시 반드시 HyunDaiEngine 클래스의 인스턴스가 필요

- Loose Coupling = 느슨한 결합
    ```
    class Car {

        private Engine engine;

        public Car(Engine engine) {
            this.engine = engine;
        }

    }
    ```
    - Car 인스턴스 생성시 Engine 인터페이스의 구현체가 필요
        - 현대, 기아 .. 등 다양한 엔진 가능
    - 유연성, 재사용성. 변경가능성 증가

- 즉 최대한 구체적인 것에 의존하지 않으면, 커플링은 느슨해진다.


## 3. What are Beans in Spring?

- 스프링 컨테이너에 의해 만들어지고 관리되는 객체
    - 컴포넌트 스캐닝이나, 직접 Bean 으로 등록된 객체
    - ApplicationContext 가 만들어서 그 안에 담고 있는 객체.
    - Singleton, 한 번 생성된다.


## 4. Bean Creation Process

- 크게 두 가지 방법으로 빈을 만든다.
    1. Component Scanning
        - @ComponentScan(@SpringBootApplication 은 내부적으로 컴포넌트 스캔을 사용함) 이 붙어있는 클래스가 있는 패키지에서부터 모든 하위 패키지의 클래스를 훑어보며 @Component 가 붙은 클래스를 찾는다.
        - 의존성이 있으면 의존성이 있는 클래스의 Bean 도 생성
        - 두 클래스 간에 연결
    2. 직접 Bean 등록
        - @Configurtation 아래에 @Bean 등록
        - @Configuration 도 어차피 @Component 를 내부적으로 사용하기에 Component 스캐닝됨.


## 5. What is the importance of the annotation @Primary

- @Component, @Bean 과 같이 쓰일 수 있다.

- Component 스캔 시 Dependency Injection 과정에서 주입할 대상이 여러개여서 자동적으로 객체를 찾지 못하는 경우가 있다.

- 예를들어 `Cleanable` 이라는 인터페이스가 있고, 
    - `Broom` 클래스가 `implements Cleanable`
    - `Tissue` 클래스도 `implements Cleanable`
    - 위 상황에서
        ```
        @Autowired
        Cleanable cleanable;
        ```
        - 이라는 코드가 있다면, `Broom` 클래스를 주입할 지, `Tissue` 클래스 주입할 지 정할 수 없다.

- 가장 높은 우선순위를 가질 아이에게 `@Primary` 어노테이션을 달면 해결 가능하다.
- 다시 말해 DI 시 여러개에 종속된 주입 대상이 있을 때 우선순위를 명시하기 위해 사용한다.


## 6. What is Dependency Injection?

- 객체의 의존성을 외부에서 주입하는 패턴

- 스프링에서는 객체의 의존성을 스프링 프레임워크에서 주입한다. 
    - IoC(Inversion Of Control), 제어의 역전
        - 프로그램 제어권을 프레임워크가 가져가는 것
        - IoC 는 DI를 통해 달성된다

- Spring 이(스프링컨테이너가) Bean 을 찾고 종속성을 식별하고 인스턴스를 생성하여 자동으로 연결하는 프로세스


## 7. Explain Inversion of Control (IOC)

- DI 랑 세트메뉴

- 개발자가 프로그램의 흐름을 주도하던 과거
    - 객체의 생성부터 생명주기를 개발자가 관리

- 스프링은 객체생명주기를 프레임워크가 주도하게 된다.
    - 객체의 생성부터 생명주기를 프레임워크가 관리

- 제어권이 넘어가서 제어의 역전

- 객체간의 결합도를 줄이고 유연한 코드를 작성할 수 있게 돕는다.
    - 내가 코드레벨에서 `A a = new A()` 해버리면 아주 강결합인데,
    - 외부에서 `A a` 를 주입받으면 한 단계 추상화가 된 것이니깐.
