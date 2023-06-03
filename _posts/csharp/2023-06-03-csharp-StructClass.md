---
title: "[C#] Class와 Struct의 차이" 

categories:
  - C Sharp
tags:
  - [C Sharp, Programming]
# 태그는 무조건 2개 이상(1개면 글이 안보임)

toc: true                         # 목차
toc_sticky: true                  # 목차 사이드바 고정

published: true                   #글 공개 여부(false해도 주소로 접근 가능)

date: 2023-06-03                  #포스팅 날짜
lastmod: 2023-06-03               #최종 수정 날짜
---

포큐 아카데미에 있는 강의 **실무 프로그래밍 입문(C#)** 를 듣고 따로 공부하면서 정리한 글입니다. 😀  
{: .notice--warning}


## 개요

`Struct`와 `Class`는 데이터를 그룹으로 묶는 방법으로서 유용하게 사용된다.
<br>
이 둘의 차이점은,

- Struct는 `값 타입(value)`이며 `Stack`에 할당되고 `값으로 복사`된다.
- Class는 `참조 타입(reference)`이며 `Heap`에 할당되고 `참조로 전달`된다.

이 둘의 차이점을 기본으로 하여 더 자세하게 알아보며 
<br>
상황별로 어떤 타입을 사용해야 메모리 관리 등 성능 측면에서 유리하게 적용될지 알아보려고 한다.

## 차이점

- new()

   일반적으로 사용자 지정 `Class`를 만들어 사용할때 new()를 통해 인스턴스는 `Heap`에 할당되고 참조로 다루어 진다.

   `Struct` 를 선언할 때도 new()를 사용하는 것을 볼 수 있는데 그러면 `Struct`도 `참조 타입` 이라고 볼 수 있을까?

  <br>

  아래 예시를 보도록 하자.

  ```c#
  public struct MyInfoStruct
  {
      public int Id;
      public int Age;

      // C#10 이후 부터 Struct도 Class 처럼 매개변수 없이 기본 생성자 형태로 사용 가능
      // 대신 모든 변수가 초기화 되어야 함
      public MyInfoStruct()
      {
          Id = 1;
          Age = 10;
      }
  }

  public class MyInfoClass
  {
      public int Id = 1;
      public int Age = 10;

      public MyInfoClass(){}
  }

  class Program
  {
      static void Main(string[] args)
      {
          MyInfoStruct structInfo = new();
          MyInfoClass classInfo = new();
      }
  }
  ```

  -<img src="/assets/images/postImages/ClassStructTest.png" style="max-width: 100%; height: auto;">


  둘다 new()를 사용하여 같은 결과가 나온 것을 볼 수 있다.
  <br>
  더 자세히 알아보기 위해 IL(중간 언어)로 디스어셈블한 결과를 보자.

  -<img src="/assets/images/postImages/ClassStructTest1.png" style="max-width: 100%; height: auto;">

  
  똑같이 new()를 사용하였지만 `Struct`의 경우 생성자를 단순히 `call` 했지만
  <br>
  `Class`의 경우 `newobj` 즉 `Heap`에 할당하여 생성 되는것을 알 수 있다.




## 어떻게 사용할까



***
<br>

    📚 혼자 공부하면서 기록한 공간입니다. 오류나 틀린 부분이 있을 경우 
    언제든지 댓글 혹은 메일로 지적해주시면 감사하겠습니다! 😉


[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}