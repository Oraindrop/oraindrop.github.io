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


## 8. IOC (Inversion of Control) Container 역할

- Bean Factory
    - Bean 생성과 관계설정, 사용, 제거 담당
    - Bean 을 찾아 의존관계를 설정하고, Bean 의 생명주기를 관리
    - 즉 Bean(오브젝트) 의 제어권 담당

- 코드레벨로 말하자면 IoC 컨테이너라고 말하는건 `ApplicationConetxt` Interface 를 구현한 클래스의 오브젝트


## 9. Application Context 는 무엇?

- BeanFactory 를 상속받고, 더 확장된 인터페이스
- IoC 컨테이너에 이미 다 말해서 별로 할말이없음
- 스프링의 IoC 컨테이너


## 10. Spring 에서 Application Context 생성하는 과정

- xml 이나 @Configuration 두 방법 중 하나로 구성
- ClassPathXmlApplicationContext 를 사용하여 생성
- ClassPathXmlApplicationContex 는 xml 파일을 찾거나, AnnotationConfigApplicationContext 를 사용하는 방법 둘 중 하나를 사용한다.


## 11. Component Scan 설명

- annotation 방법이면 @ComponentScan 위치(base 패키지) 아래에 있는 모든 @Component 어노테이션을 검색
- xml 방법이면 <context : component-scan base-package =”com.demo.compscanex”/> 이런걸 쓴다고 함.


## 12. Spring Boot 에서 Component Scan 은 어떻게?

- 보통 @SpringBootApplication 이라는 어노테이션을 사용한다
    - @ComponentScan과 @EnableAutoConfiguration 이 포함되어 있음
    - 이 클래스 위치가 base package 가 되어 쭈욱 스캔


## 13. @Component, @Repository @Service @Controller 비교

- @Controller, @Service, @Repository 어노테이션은 @Component 어노테이션을 더 읽기 쉽도록 특정 상황에 Specific 하게 정의해 놓은 것

- 예를들어 MVC 관점에서 클라이언트 통신의 최초시점은 @Component를 사용하는 대신 @Controller를 사용
비지니스 로직을 담당하는 곳은 @Service, 일반적으로 DAO 라고 부르는 데이터베이스와 상호작용이 일어나는 부분은 @Repository를 사용하여 가독성을 높인다.


## 14. Spring Bean Scopes

- 스프링에서 Bean으로 지정된 객체는 기본적으로 싱글톤 객체로 관리된다.

- 하지만 요구사항 과 구현기능 등의 필요에 따라서 비싱글톤이 필요한 경우도 있고, 이를 명시적으로 구분하기 위하여 scope 이라는 키워드를 제공한다.

- singleton, prototype, request, session 정도가 있다.

- singleton
    - 디폴트
    - bean 은 spring 컨테이너에서 한 번 생성된다.
    - 생성된 인스턴스를 single beans cache 에 저장하고, 요청과 참조가 있으면 캐시된 객체를 반환한다.

- prototype
    - bean 의 모든 요청에 새로운 객체를 생성한다.
    - 즉, 의존성 관계의 bean에 주입될 때 새로운 객체가 생성되어 주입된다.
    - 정상적인 방식으로 gc에 의해 bean이 제거된다.

- request
    - HTTP Request 별로 새로운 객체를 생성하고, 요청이 끝나면 소멸한다.

- session
    - HTTP 세션 별로 새로운 객체를 생성하고, 세션이 끝나면 소멸한다.


## 15. DI 종류

- Setter Injection, Constructor Injection, Field Injection

- 피부에 와닿는 큰 장점을 느끼지 못해서 Field Injection 을 계속 사용하고 있으나 `Constructor Injection` 을 가장 클린코드에 가깝다고 생각하는 분위기인 것 같음

- 여러 이유가 있겠지만, 객체 생성시점에 순환참조가 일어나는 지 확인할 수 있다는 장점은 수긍이 되었고, 테스트코드 생성 시에도 조금 이점이 있다는 것도 이해는 되었다!

- Field Injection 은 매번 편하다는 것 빼고 장점이 없다는 글들이 많이 보이는데, 편리한것도 엄청 큰 장점 아닌가..?


## 16. Configuration XML vs Annotation

- Configuration 설정하는 두 가지 방법
- xml 은 길다. 매번 많은 설정 파일을 작성해야함.
    - `xml` 을 사용한다는 것 자체가 장점이 될 수 없을 것 같음
    - 외부에 존재하고, 필요한 경우 이미 빌드한 결과에 재 컴파일 없이 xml 만 수정하여 변경사항을 적용할 수 있다는 장점이 있음
- annotation 은 코드레벨로 확인할 수 있다는 점이 쉽게 판단할 수 있어 좋고, 코드의 양도 적은 편


## 17. Spring 의 Autowiring 방식들

- byName, byType, constructor, no

- byName : Spring 컨테이너가 이름과 동일한 빈을 찾는다.

- byType : Spring 컨테이너가 클래스가 동일한 빈을 찾는다.

- constructor : Spring 컨테이너가 생성자 기반으로 autowiring 한다.
    - 방식은 byType 과 유사, 생성자의 arguments 클래스 유형을 찾은 다음 주입.

- no : autowiring 을 사용하지 않는다.

- SpringBoot 를 사용하고 있는 내가 @Autowired 해서 쓰는 건 byType으로 사용하고 있는 것이려나

- @Qualifier 를 사용해서 name 을 지정해주면 byName 이 되는느낌


## 18. Dirty Read 가 무엇?

- 커밋되지 않은 데이터를 읽는 것이지 뭐

- 하나의 트랜잭션 T1이 다른 트랜잭션 T2에 의해 변경된, 하지만 아직 커밋되지 않은 변경사항을 read 하는 것

- dirty read 의 위험성은 이게 롤백될 수도 있는 데이터이기때문


## 19. Spring 4.0 과 5.0 의 새로운 기능

- 4.0 이 Java 를 최초로 지원했고, 5.0 이 Reactive Programming 및 Kotlin 을 지원했다는데.. 그냥 대충 넘어가겠음


## 20. Front Controller 란?

- Front Controller = DispatchServlet 

- HTTP Request 를 최초로 받는 친구

- 매핑된 다른 컨트롤러로 위임


## 21. ViewResolver 란?

- 컨트롤러가 최종적으로 결과를 출력할 View를 리턴할 때, View 를 직접 리턴할 수 도 있지만, 매번 View를 생성하는 것은 비효율 적이므로 View 의 논리적인 이름인 String 만 return

- DispatchServlet 의 View Resolver가 이를 이용해 View Object 를 만들어 return

- 정리하면 ViewResolver 란 논리적인 이름과 맵핑되는 View 를 생성해주는 애. 라고 보면 될 듯

- SpringBoot 에서도 일반적인 API 같은 json return 에서 그냥 @Controller를 사용할 경우 (@ResponseBody 도 사용하지 않을 경우) 역시 ViewResolver(BeanNameViewResolver) 에서 ViewName 에 해당하는 뷰를 찾으려고 시도

- @RequestBody, @ResponseBody 가 있을 경우 `HttpMessageConverter` 를 사용한다. @RestController 에서는 @ResponseBody 가 자동으로 적용된다고 보면 됨.

- 즉, @Controller
    - Client -> DispatcherServlet -> Handler Mapping -> **Controller** -> **ViewResolver** -> **View**

- @RestController
    - Client -> DispatcherServlet -> Handler Mapping -> **MessageConverter** -> **HTTP Response** (application/json, text/plain 등)
    - 대표적인 MessageConverter
        - application/json : MappingJackson2HttpMessageConverter 
        - application/xml 또는 text/xml : MarshallingHttpMessageConverter


## 22. Spring MVC 처리과정

1. Client 의 Request 를 DispatcherServlet 이 받는다.

2. DispatcherServlet 은 HandlerMapping 을 통해 적절한 Controller 를 찾아 위임한다.

3. Controller 는 필요에 따라 의존성 주입된 Service 와 DB 데이터를 활용하여 결과를 만들어 Model 에 넣고 View Name 을 반환한다.

4. DispatcherServlet 은 ViewResolver 를 활용하여 View 를 만들어 Response.


## 23. @ModelAttribute 란?

- 클라이언트의 Request Parameter 들을 객체로 바인딩 시키고, View 로 넘겨 사용 및 출력하기 위해 사용.

- @RequestBody 의 경우 Http Request 의 Body 부분을 MessageConverter 를 활용하여 Java Object로 변환한다.

- @ModelAttribute 는 완전 자바빈규약 즉 setter 만 활용하여 바인딩. 즉 얘는 세터가 없으면 매핑이 아예되지가 않음.


## 24. @SessionAttributes 란?

- Model 정보를 HttpSession 에 저장

- 클래스에 다는거라 Controller 에 주로 다는 것으로 보임

- 컨트롤러에서 메서드가 생성하는 모델정보 중에서 @SessionAttirbutes("key") 라고 명시한 key 값과 동일한 것을 세션에 저장



## 25. @InitBinder 란?

- 특정 컨트롤러에서 바인딩 또는 검증 설정을 변경하고 싶을 때 설정값을 지정하려 사용



## 26. @ControllerAdvice 란?

- 여러 Controller 전반에 걸쳐 어떤 로직을 적용하고 싶을 때 사용

- 보통은 @ExceptionHandler 와 함께 에러처리를 묶어서 하려 사용
    - @ExceptionHandler 말고 @ModelAttribute, @InitBinder 도 사용할 수 있음

- 별도 설정하지 않으면 모든 Controller 대상


## 27. Why Spring Boot?

- 스프링 어플리케이션을 구동하기 위해 많은 설정들이 자동으로 되어있어, 신속하게 설정할 수 있다.

- 스프링부트의 AutoConfiguration 은 클래스 패스(경로)에 특정 클래스가 있는지 감지한 후 자동으로 모든 설정을 구성함.

- 또 SpringBoot 의 특성으로는 내장 Tomcat


## 28. Spring vs Spring MVC vs Spring Boot

- Spring
    - Spring 프레임워크의 가장 중요한 특징은 의존성 주입(DI, Dependency Injection)
    - 모든 스프링 모듈들의 핵심에는 의존성 주입이나 IOC(Inversion of Control)가 있다.

- Spring MVC
    - Spring MVC 프레임워크는 **웹 애플리케이션 개발 방법** 을 제공한다. 
    - Dispatcher Servlet, ModelAndView, View Resolver 과 같은 개념을 이용해서 웹 애플리케이션 개발을 쉽게 할 수 있도록 해준다.

- SpringBoot
    - SpringBoot 가 해결한 핵심은 쉽게 애플리케이션을 구동할 수 있는 것.
    - **Auto Configuration**
        - 클래스패스상에 사용가능한 프레임워크와 환경설정을 바라본다. 
        - 이것들을 기반으로 스프링 부트는 애플리케이션을 이 프레임워크들과 함께 구성하는데 필요한 기본 환경설정을 제공한다.


## 29. @SpringBootApplication?

- `@SpringBootApplication` 은 세 가지 어노테이션을 포함한다.
    - @SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan

- 참고
    ```
    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan(
        excludeFilters = {@Filter(
        type = FilterType.CUSTOM,
        classes = {TypeExcludeFilter.class}
    ), @Filter(
        type = FilterType.CUSTOM,
        classes = {AutoConfigurationExcludeFilter.class}
    )}
    )
    public @interface SpringBootApplication { ... }
    ```

1. @ComponentScan
    - 해당 어노테이션이 선언된 하위 패키지에서 @Componet 관련 어노테이션을 찾아 Bean 으로 등록한다.

2. @EnableAutoConfiguration
    - org.springframework.boot:spring-boot-autoconfigure 의 META-INF 에 spring-factories 파일이 있고, Key 값들과 많은 Class 들을 가지고 있다.
    - 그들은 @Configuration이라는 Annotation 을 가지고 있고, 이런 명시된 class 들이 AutoConfiguration 의 대상이 된다.

3. @SpringBootConfiguration
    - 그냥 Configuration 과 비슷한 듯.
    - SpringBoot 의 Configuration 이다. 정도?
        - 테스트 어노테이션을 사용할 때 계속 이 어노테이션을 찾기 때문에 스프링 부트에서는 필수 어노테이션이다. 라고 함.


## 30. SpringBoot 의 Embedded Server 의 의미

- 별도의 WAS 없이, 독립적으로 실행 가능한 Jar.

- SpringBoot 의 AutoConfiguration 에 Embedded Server(Tomcat) 설정과 구동이 포함되어 있기 때문.
    - org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration.java


## 31. application.properties

- 이 파일은 스프링부트가 애플리케이션을 구동할 때 자동으로 로딩하는 파일.

- key - value 형식으로 값을 정의하면 애플리케이션에서 참조하여 사용할 수 있다.

- 스프링 부트 사용 시 필요한 옵션들을 작성/추가 할 수 있다.
    - 이를테면 DB 정보, 로깅 정보, 톰캣 스레드 설정 등등


## 32. What is Spring JDBC

- JDBC
    - 자바에서 데이터베이스에 상호작용을 할 수 있도록 하는 일종의 표준

- Spring JDBC
    - JDBC 를 쉽게 쓸 수 있도록 도와주는 라이브러리
    - 반복적인 작업 (Connection, Statement, ResultSet ..) 을 대신해줘서 개발자는 필요한 부분, 핵심 로직만 개발하면 된다.
    

## 33. JDBC vs Spring JDBC

- 위에서 해버렸다.

- Spring JDBC 는 기존 JDBC 의 반복적인 작업을 상당수 대신한다.
    - Connection 연결 객체 생성/종료
    - Statement 준비/실행/종료
    - ResultSet 처리/종료/예외처리 등등


## 34. JPA 란?

- JPA 를 알려면 먼저 ORM 을 알아야함.

- ORM 이란
    - 객체를 DB에 맵핑해주는 거
    - Object 와 RDB 패러다임의 불일치를 개발자 대신 해결해준다.
    - 쉽게말해, SQL Query 를 사용하지 않고 다분히 직관적인 코드로 데이터를 조작할 수 있다.

- JPA 는 Java 진영의 ORM 기술표준으로, 인터페이스의 모음이라고 생각하면 됨.


## 35. Hibernate 란?

- JPA 인터페이스의 구현체
- ORM 프레임워크 중 하나


## 36. Constructor Injection 과 Setter Injection Case 설명

- Setter Injection
    - 상황에 따라 의존성 주입이 가능하다.
    - 즉 선택적인 의존성 사용 시 유용하다.
    - 과거의 스프링(3.x) 에서는 Setter Injection 을 추천했다.

- Constructor Injection
    - 필수적인 의존성 주입에 유용하다.
    - 순환 의존성도 알 수 있다.
    - 현재 스프링에서는 Constructor Injection 을 추천한다.


## 37. pom.xml 이란?

- Maven 프로젝트의 구성이 정의된 문서


## 38. @RequestParam?

- http request 의 parameter 를 서버에서 데이터를 읽고 바인딩 할 때 사용하는 annotaion


## 39. Spring Security 란?

- Spring Security는 Spring 기반의 애플리케이션의 보안(인증과 권한, 인가 등)을 담당하는 스프링 하위 프레임워크이다. 

- 인증과 권한에 대한 부분을 `filter` 흐름에 따라 처리하고 있다. (not Interceptor)

- 보안과 관련된 많은 옵션들을 제공해주어 개발자 입장에서는 일일히 보안 관련 로직을 작성하지 않아도 된다는 장점