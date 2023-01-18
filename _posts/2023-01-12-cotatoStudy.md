---
layout: post
title: "스프링의 개요 알아보기"
subtitle: 스프링 1주차
categories: spring
tags: [spring]
---

## What is Spring Boot

요즘 많은 사람이 파이썬으로 프로그래밍을 합니다.\
그럼에도 서버 사이드에서 많이 쓰이는 언어와 프레임워크는 `JAVA`와 `Spring`이다.\
Spring은 어떠한 강점이 있기 때문에 많은 서버 개발자들이 스프링을 선택하는 것일까?

### 1. AOP
스프링은 관점 지향 프로그래밍이 가능하다.

어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화 한다는 의미이다.[^1]

흩어진 관심사(Crosscutting Concerns)라는 개념으로 이해할 수 있다.

위 개념은 소스 코드 상에서 `반복적으로 쓰이는 코드를 모듈화` 하는 행위인데 이 방법을 통해 효율적으로 `재사용`하는 프로그래밍을 말한다.

- 프록시 기반 AOP 구현체
- 스프링 Bean에 AOP 적용
- 스프링 IoC(제어 역전)와 연동

### 2. DI, IoC
Dependency Injection(의존 관계 주입)

객체를 직접 생성하는 것이 아닌 `외부에서 생성한 후 주입`시키는 방식이다.

ex) setter를 통한 주입

IoC는 제어 역전이라고 말하며 말 그대로 `제어의 흐름을 바꾼다.`

객체의 의존성을 역전시켜 객체 간의 결합도를 줄이고 가독성 및 코드의 중복, 유지보수를 편하게 할 수 있다.[^2]

|기존 객체 생성 순서|스프링의 객체 생성 순서|
|---|---|
|1. 객체 생성|1. 객체 생성|
|2. 의존성 객체 생성|2. 의존성 객체 `주입`|
|3. 의존성 객체 메소드 호출|3. 의존성 객체 메소드 호출|

위의 표와 같이 스프링이 모든 의존성 객체를 만들어 주고 필요한 곳에 주입한다.

스프링에서는 객체를 Bean으로 부르고 Bean을 관리하는 공간을 Bean 컨테이너라고 한다.

➡️ Bean은 싱글톤 패턴을 가지고 스프링이 제어의 흐름을 가져간다.

### 3. POJO(오래되고 간단한 자바 객체)
Java EE -> 사용하기 간편하지만 중량의 프레임 워크이고 

java의 핵심이 객체 지향임에도 불구하고 특정 기술, 환경에 종속되어 가독성, 유지보수, 확장성이 모두 떨어지는(객체지향성이 감소하는) 현상을 비판하기 위한 개념[^3]

아래와 같은 클래스가 POJO의 예시입니다.
```java
public class Human{
    private String name;
    private int age;
    public String getName(){return name;}
    public int getAge(){return age;}
    public void setName(String name){this.name = name;}
    public void setAge(int age){this.age = age;}
}
```
`Hibernate`가 바로 POJO기반의 Persistence 프레임워크이다.

하이버네이트를 생각해보면 어떤 DB에 종속하지 않는다는 특징이 바로 떠오릅니다.

또한 JPA가 매우 객체 지향적인 성질을 가진다는 것을 떠올리면 됩니다.

### Spring vs Spring Boot
그렇다면 스프링과 스프링 부트는 무슨 차이가 있을까요?

스프링은...
1. sub-frameworks의 총체
    - AOP, ORM, Web Flow, Web MVC..등 여러 계층으로 이루어짐
2. POJO, DI 등 enterprise application을 개발하는데 최적화됨
3. 기존 classic Java frameworks와 APIs(JDBC, JSP, Java Servlet)보다 쉬운 개발

등등의 특징을 가지고 있습니다.

스프링 부트는...
1. 전통적인 스프링 프레임워크의 위에 만들어졌습니다.
    - 즉 스프링의 모든 특징을 가지며 쉽게 사용할 수 있습니다.
2. 스프링 부트는 `마이크로서비스` 기반 프레임워크이고 빠른 시간에 개발이 가능합니다.
3. 스프링 부트의 모든 것은 `자동으로 설정`됩니다.

(micro service architecture) : `각각을 마이크로하게 나눈 독립적인 서비스를 연결한 구조`

스프링 부트를 사용하는 이유는 `스프링의 장점을 가지며 더욱 간단하고 빠르게 개발을 하기 위해` 입니다.

## What is Gradle?
이제 스프링 부트가 얼마나 좋은 강점이 있는지 알겠습니다.\
`IntelliJ`라는 개발환경에서 첫 프로젝트를 생성하려고 하는데...

```
프로젝트 옵션 : 
Maven, Gradle, Ant
```
위 옵션들은 무엇이고 어떠한 차이가 있을까요?[^4]

### 빌드 도구들의 등장
python, js를 사용해본 사람이라면 패키지 매니저를 잠시 떠올려 보자.
```bash
pip install numpy
npm install axios
```
pip, npm등 이 떠오른다. 그렇다면 java는 어떤 것을 사용할까?\
이때 Maven과 Gradle의 필요성이 짐작이 간다.

Maven과 Gradle의 특징표[^5]

||Maven|Gradle|
|---|---|---|
|특징|오픈소스 빌드 도구(안정성에 집중)|오픈소스 빌드 도구|
|작동|XML을 통해 프로젝트 구조를 생성|Java와 Groovy기반 DSL로 작동한다.|
|목적|한정된 시간에 어플리케이션을 개발하는 것에 초점|어플리케이션에 기능을 추가하는 것에 초점|
|성능|느리고, 임시파일을 생성하지 않는다|maven보다 빠르고 현재 구동중인 작업에 초점|
|복잡도|복잡하다|단순하다|
|커스터마이즈|customizable하지 않고 소수의 IDE에만 기능 제공|customizable하고 다양한 IDE지원|
|지원언어|Java, Scalar, C#, 루비 개발을 지원|Java, C, C++, Groovy에 개발 지원|

```
Groovy -> Java에 파이썬, 루비, 스몰토크 등의 특징을 더한 동적 객체 지향 프로그래밍언어
DSL -> 이 본문에서는 Digital Subscriber Line이 아닌 
    Domain specific language로 어플리케이션 도메인을 명시하는데 특화된 언어.
    GPL(general purpose language)와 정반대되는 성격을 가진다.
    ex) Java에서는 ANT, Maven, HQL
        SQL, CSS, 정규식, Make...
        HTML도 도메인 특화 언어이다.
GPL 예시 : java, C(java, C는 하나의 도메인에만 얽혀있지 않는다.)

의존성 : 프로젝트에 필요한 라이브러리.
```
## Plugin은 무엇인가요?

Gradle의 특징중 하나가 떠오릅니다.\
또한 책에서 본 gradle 중 어느 요소가 떠오릅니다.
```
Gradle is a tool used for creating plug-ins and is a flexible instrument.

plugins {
    id 'java'
}
```
> 플러그인은 특정 작업을 하기 위해 모아놓은 Task의 묶음이다.[^6]

그러므로 Gradle에서 Plugin은 Gradle Task의 집합입니다.


스프링 어플리케이션을 개발에는 아래와 같은 과정(Task)이 이루어진다.
1. 필요한 라이브러리를 받아온다.
2. 라이브러리로 자바 코드를 컴파일
3. 컴파일된 바이트코드의 묶음을 패키징

위와 같은 작업들을 일일히 Gradle 파일에 정의하면 매우 비효율적이다. (매번 비슷한 작업을 똑같이 작성해야하기 때문)

그래서 IDE에서 플러그인으로 위와 같은 작업을 정의하고 작업을 Gradle파일에 등록한다.

> gradlew는 무엇인가요?

gradle-wrapper의 약어이다.

개발자가 새로운 환경에서 개발을 시작하더라도 gradle을 설치할 필요 없이 동일한 환경에서 실행할 수 있도록 해주는 내장 gradle이다.

지금까지 Spring의 특징과 Spring의 구성요소를 간략히 알아보았습니다.

다음에는 Spring의 구성요소를 면밀히 알아보고 Hibernate의 대략적인 이해를 하도록 하겠습니다.

[^1]:https://engkimbs.tistory.com/746
[^2]:https://velog.io/@gillog/Spring-DIDependency-Injection
[^3]:https://dev-coco.tistory.com/82
[^4]:https://sugerent.tistory.com/571
[^5]:https://www.geeksforgeeks.org/difference-between-gradle-and-maven/
[^6]:https://kotlinworld.com/323