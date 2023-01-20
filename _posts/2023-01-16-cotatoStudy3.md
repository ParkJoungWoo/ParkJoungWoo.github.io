---
layout: post
title: "Stream 문법 알아보기"
subtitle: 스프링 2주차
categories: spring
tags: [spring]
---

## Stream 개요
Stream은 java8에서 부터 시작된 Collection, Array등 저장 요소를 하나씩 참조하며 함수형 인터페이스(Lambda)를 적용하여 반복적으로 처리할 수 있는 기능[^1]

I/O Stream이 아닌 저장 요소의 Stream

### 왜 이걸 사용하는 거죠?
1. 단순히 for문을 대신하는 게 아닌 for문의 반복 행위만 대신한다.
   - for문을 대신하는 것만 아닌 다른 기능(대소비교, 매칭 등등)을 같이 수행
2. 함수형 프로그래밍이 가능하다.
   - 파이프라인 구조로 직관적인 프로그래밍이 가능

위와 같은 장점으로 많은 Java 개발자가 Stream API를 사용한다.

### 기본적인 구성
```
객체집합(Collection).스트림생성().중개연산().최종연산();
```
스트림 생성법
```java
public static void main(){
    List<String> users = Arrays.asList("A", "B", "C");
    users.stream();

    int[] numbers = {1,2,3,4};
    Arrays.stream(numbers);

    Stream<Integer> stream = Stream.of(1,2,3,4);
}
```
중개 연산방법
```java
public static void main(){
    //Filter 사용법
        // 조건에 맞는 요소
    int check = (int) users.stream()
        .filter(h->h.contains("C"))
        .count();
    System.out.println("필터링 : " + check);
    //Map 사용법
        Arrays.stream(numbers)
            .map(x -> x+5000)//int형 자료에 대한 연산
            .forEach(x -> System.out.println("5000을 더해 " +x+"를 출력하자"));//반복문
        
        // 원하는 컬렉션 형태로 파싱하기
        List<Integer> newList = Arrays.stream(numbers)
                                    .map(x->x+2000)
                                    .boxed()
                                    .collect(Collectors.toList());
    //sort
    int a[] = Arrays.stream(numbers)
                        .sorted()
                        .toArray();
    Arrays.stream(a).forEach(System.out::println);
    // 개수 제한
        System.out.println("5개만 출력-------------");
        Arrays.stream(a).limit(5).forEach(System.out::println);

    //distinct
    List<Integer> list_1 = Stream.of(1,1,1,2)
            .distinct()
            .collect(Collectors.toList());
    list_1.forEach(System.out::println);
}
```
위와 같이 map, distinct 등 기능을 stream에 적용한다.

최종 연산방법
```
.forEach
.get()
.getInt()
.collect(Collectors.toList())
```
stream을 위와 같은 리턴 타입으로 바꿔주는 역할을 한다.

전체 소스코드 
```java
import java.util.*;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
import java.util.stream.IntStream;
import java.util.Comparator;
import java.util.stream.Collectors;

public class StreamTester {
    public static void main(String[] args) {
        // 선언부
        List<String> users = Arrays.asList("A", "B", "C");
        users.stream();

        int[] numbers = {5,6,7,8,1,2,3,4};
        Arrays.stream(numbers);

        Stream<Integer> strm = Stream.of(100,200,300);

        // 필터링
            // 조건에 맞는 요소
        int check = (int) users.stream()
            .filter(h->h.contains("C"))
            .count();
        System.out.println("필터링 : " + check);

            // distinct한 요소
        List<Integer> list_1 = Stream.of(1,1,1,2)
                .distinct()
                .collect(Collectors.toList());
        list_1.forEach(System.out::println);

        // 개수 세기
        check = (int) users.stream().count();
        System.out.println("개수 : " + check);

        // 최댓값 찾기
        check = Arrays.stream(numbers).max().getAsInt();
        System.out.println("최댓값 : " + check);

        // 최솟값 찾기
        check = strm.min(Comparator.comparing(x-> x)).get();
        System.out.println("최솟값 : " + check);

        // 평균
        double dd = Arrays.stream(numbers).average().getAsDouble();
        System.out.println("평균값 : " + dd);

        // 첫번째 요소
        Stream<Integer> strm1 = Stream.of(1100,1200,1300);
        check = strm1.findFirst().get();
        System.out.println("첫번째 요소 : " + check);

        // 매핑(mapping)
        Arrays.stream(numbers)
            .map(x -> x+5000)//int형 자료에 대한 연산
            .forEach(x -> System.out.println("5000을 더해 " +x+"를 출력하자"));//반복문
        
        // 원하는 컬렉션 형태로 파싱하기
        List<Integer> newList = Arrays.stream(numbers)
                                    .map(x->x+2000)
                                    .boxed()
                                    .collect(Collectors.toList());
        // 삽질 -> Collectors가 Stream의 Collectors인데 util의 Collector를 import 했다..
        newList.forEach(System.out::println);

        // 누적 합
        check = Arrays.stream(numbers)
                        .reduce((x,y)-> x+y)
                        .getAsInt();
        System.out.println("누적 합 :" + check);

        // 정렬
        int a[] = Arrays.stream(numbers)
                        .sorted()
                        .toArray();
        Arrays.stream(a).forEach(System.out::println);
        // 개수 제한
        System.out.println("5개만 출력-------------");
        Arrays.stream(a).limit(5).forEach(System.out::println);
    }
}
```

https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html

위와 같이 쓰임새가 매우 다양하고 편리하므로 `공식문서`를 찾아 기능을 숙지하는 것이 중요하다.


[^1]:(https://jeong-pro.tistory.com/165)
