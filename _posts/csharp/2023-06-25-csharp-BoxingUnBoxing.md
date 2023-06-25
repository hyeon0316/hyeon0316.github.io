---
title: "[C#] Boxing, UnBoxing" 

categories:
  - C Sharp
tags:
  - [C Sharp, Programming]
# 태그는 무조건 2개 이상(1개면 글이 안보임)

toc: true                         # 목차
toc_sticky: true                  # 목차 사이드바 고정

published: true                   #글 공개 여부(false해도 주소로 접근 가능)

date: 2023-06-25                  #포스팅 날짜
lastmod: 2023-06-25               #최종 수정 날짜
---


## 개요

C#과 같은 .Net FrameWork 언어들은 모든 타입이 `System.Object` 로 부터 파생되어 설계되어 있다.

즉 간단한 예시로 보면 이런식으로 사용 가능하다.

```c#
int a = 5;
Object o = (Object)a; //boxing, (Object) 생략 가능

int b = (int)o; //unBoxing
```

- Boxing

    int 는 `Object`의 `상속관계`이기 때문에 `Object`형으로 변환 가능하다. 이 과정을 `Boxing` 이라고 한다. `Object`형은 기본적으로 참조 형식인데, `Boxing` 한다는 의미는 `값 형식`의 int 형을 `참조 형식`으로 변환 한다는 의미다.

- UnBoxing

    그와 반대 개념인 `UnBoxing`은 `Boxing`된 `참조 형식`을 다시 `값 형식`으로 변환하는 과정이다.
    `UnBoxing`은 int b = (int)o; 와 같이 명시적으로 타입을 지정해야 하며 `UnBoxing`은 기본적으로 `Boxing` 된 데이터에 대해서만 수행 가능하다. (오류 발생)


## 성능 문제

`Boxing`과 `UnBoxing`을 사용하여 `값 형식`의 다형성을 활용하거나 등으로 사용할 수 있지만 그 과정에서 성능 문제가 발생할 수 있다.

- Boxing 할때

    - `값 형식`을 `참조 형식`으로 변환할때 `Stack`에 있는 데이터를 `Heap`에 할당하므로 메모리 사용량이 증가한다.

- UnBoxing 할때

    - `참조 형식`을 `값 형식`으로 변환할때 `Heap`에 있던 데이터를 `Stack`으로 복사한다. 이는 추가적인 연산 비용을 발생시킨다.
    
    - 변환하려는 형식이 `Boxing`했던 값인지 확인하기 위해 런타임 중에 유형 검사가 필요하므로 추가적인 연산 비용을 발생시킨다.

`Boxing`과 `UnBoxing`을 자주 한다는 것은 메모리의 복사가 빈번하게 일어난다는 의미이다.


## Generic

`Boxing`과 `UnBoxing`의 성능적인 문제 때문에 `Generic`을 사용하는 것이 권장되고 있다.

우리가 흔히 사용하는 제네릭 컬렉션중 하나인 List<T>의 예시를 보자.

```c#
List<int> numbers = new List<int>(); //List<int>, 미리 타입 지정
numbers.Add(10); // boxing 없이 int 값을 저장

int firstNumber = numbers[0]; // unBoxing 없이 int 값을 읽음
```

기본적인 `Boxing`, `UnBoxing` 과 달리 `Generic`은 컴파일 시점 부터 어떤 타입인지 정보를 알고 있기 때문에 `값 형식`과 `참조 형식`간의 변화에 대한 과정이 필요하지 않다. 이는 곧 성능 향상에 이어지며 메모리 복사나 수명 관리와 같은 추가 연산 작업을 줄일 수 있다.



## 참고 자료

- [Boxing 및 Unboxing(C# 프로그래밍 가이드)](https://learn.microsoft.com/ko-kr/dotnet/csharp/programming-guide/types/boxing-and-unboxing)



***
<br>

    📚 혼자 공부하면서 기록한 공간입니다. 오류나 틀린 부분이 있을 경우 
    언제든지 댓글 혹은 메일로 지적해주시면 감사하겠습니다! 😉


[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}