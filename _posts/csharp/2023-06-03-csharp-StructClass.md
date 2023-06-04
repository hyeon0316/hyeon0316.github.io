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

Microsoft 공식 문서를 참고하여 따로 공부하면서 정리한 글입니다. 😀  
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

## 할당 방식

- new()

   일반적으로 사용자 지정 `Class`를 만들어 사용할때 `new()`를 통해 인스턴스는 `Heap`에 할당되고 참조로 다루어 진다.

   `Struct`를 선언할 때도 `new()`를 사용하는 것을 볼 수 있는데 그러면 `Struct`도 `참조 타입`이라고 볼 수 있을까?


  아래 예시를 보도록 하자.

  ```c#
  public struct MyInfoStruct
  {
      public int Id = 1;
      public int Age = 10;

      // C#10 이후 부터 Struct도 Class 처럼 매개변수 없이 기본 생성자 형태로 사용 가능
      // 대신 모든 변수가 초기화 되어야 함
      public MyInfoStruct(){}
  }

  public class MyInfoClass
  {
      public int Id;
      public int Age;

      // Class는 필요에 따라 초기화 가능
      //기본 0으로 초기화
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

  <img src="/assets/images/postImages/StructClassTest.png" style="max-width: 100%; height: auto;">


  둘다 new()를 사용하여 쉽게 예상했던 결과가 나온 것을 볼 수 있다.
  <br>
  더 자세히 알아보기 위해 IL(중간 언어)로 디스어셈블한 결과를 보자.

  <img src="/assets/images/postImages/StructClassTest1.png" style="max-width: 100%; height: auto;">

  
  똑같이 `new()`를 사용하였지만 `Struct`의 경우 생성자를 단순히 `call` 했지만
  <br>
  `Class`의 경우 `newobj` 즉, `Heap`에 할당하여 생성 되는것을 알 수 있다.

  
<br>

 - 예외 상황
    
    `Struct`는 `Stack`영역에, `Class`는 `Heap` 영역에 할당 된다는 것을 알았을때
    <br>
    만약 `Class` 안에 `Struct`를 담고 있는 경우 각자의 영역에 할당 될까?

    아래 예시를 보도록 하자.

    ```c#
    public struct MyInfoStruct
    {
        public int Id;
        public int Age;

        public MyInfoStruct()
        {
            Id = 1;
            Age = 10;
        }
    }

    public class MyInfoClass
    {
        public int Id;
        public int Age;

        //어떤 영역에 할당 될까?
        MyInfoStruct structInfo = new();    
    }

    class Program
    {
        static void Main(string[] args)
        {
            MyInfoClass classInfo = new();
        }
    }
    ```

    다음과 같이 발생한다.
    <br>
    <img src="/assets/images/postImages/StructClassTest2.png" style="max-width: 100%; height: auto;">

    `Class` 안에 `Struct`를 선언하면 `Struct`는 `Class` 객체의 일부로 간주되어 메모리에 저장 된다.
    <br>
    이때 `Class`의 인스턴스가 제거되면 그 안에 포함된 `Struct`도 함께 제거된다.



## 어떻게 사용할까

### 해제 방식

- `Stack`에 할당되는 `Struct`는 인스턴스 사용이 끝나면 자동으로 메모리에서 해제된다.

- `Heap` 에 할당되는 `Class`는 C#의 `가비지 콜렉터(GC)`에 의해 관리되며, 더 이상 사용되지 않을 때 자동으로 메모리에서 해제된다.

  - 즉, 생명 주기는 해당 인스턴스를 참조하는 모든 참조들이 없어질 때 까지 유지된다.

### 정리

- `Struct`는 주로 작은 크기의 데이터를 표현하고, 불변성을 유지해야 할때 사용한다.

    - `Stack` 메모리는 크기 제한이 있어 많이 사용할 경우 `StackOverFlow`가 발생(C#에서는 최대 16Byte 까지 가능) 
    - `Class`로 작은 크기의 데이터를 만들 경우 `Heap`에 할당된 메모리는 `가비지 콜렉터(GC)`에 의해 정리되는데 `가비지 콜렉터(GC)`가 처리해야할 양이 많아지면 성능저하가 발생한다.(이 경우 `Class` 보단 `Struct` 권장)

- `Class`는 크거나 복잡한 데이터를 다룰 때 사용한다.

    - `Struct` 에 비해 `상속`이라는 것이 가능하기 때문에 더 다양한 구조를 만들 수 있다.
    - Class를 사용하여 `가비지 콜렉터(GC)`의 처리 양이 많아지면 프로그래머가 `메모리 사용 패턴`과 `가비지 생성`을 최적화 하는 것이 중요하다.




***
<br>

    📚 혼자 공부하면서 기록한 공간입니다. 오류나 틀린 부분이 있을 경우 
    언제든지 댓글 혹은 메일로 지적해주시면 감사하겠습니다! 😉


[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}