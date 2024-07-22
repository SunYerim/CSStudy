## Interned String in Java



## prerequisite

* **Same String have Same memory**

  * `같은 값을 가지면 굳이 다른 메모리에 분리하여 저장하는 것이 아닌, 하나의 메모리 주소에 적어두고 해당 메모리 주소를 가지도록 하는 것`

  * ```java
    String s1 = "hello";
    String s2 = new String("hello"); // interning X
    // s1 와 s2는 다른 메모리 주소를 가리키고 있음
    String s3 = new String("hello").intern();
    String s4 = "hello";
    String s5 = String.valueOf("hello");
    // (주소) s1 = s3 = s4 = s5
    ```

* String이 immutable하게 설계된 이유와 연관이 있다.

  * 동시성

    * immutable 객체는 race-condition 고려 불필요

  * 성능

    * 동일한 값을 가진 string literal을 매 번 새로운 메모리에 할당하는 것은 비효율적

  * 보안

    * toctou 이슈에 대해서 고려하지 않아도 됨

  * 캐싱

    * hashcode caching

    * `hashCode()` 를 한 번 캐싱해두면, 다음 접근시에도 항상 동일한 hashCode를 반환하는 것이 보장되므로 이를 캐싱하여 성능상의 이점을 노릴 수 있음

      

## Intern in Java String

JVM의 constant pool에서 String Literal을 조회한다.

1. 있다면? 반환
2. 없다면? constant pool에 등록하고 반환



String Literal의 불변성을 유지하기 위해서 사용되는 메소드

$\rightarrow$ `String.intern()`



[under the hood](https://www.latera.kr/blog/2019-02-09-java-string-intern/)

## 면접 예상 질문

* Java에서는 어떻게 String을 관리하나요?
  * Java에서는 String literal에 대해서 constant pool이라는 Heap내부의 공간에 문자열 데이터를 저장한다음, 지역 변수에서 이를 참조하는 방식으로 동작합니다.
  * java의 String은 기본적으로 불변성을 고려하여 설계되었습니다. 이러한 특징을 잘 활용하기 위해서, constant pool 내부에 string literal을 생성 or 참조하는 방식으로 사용하여야합니다. 대표적인 String 값을 가진 객체를 가져오는 메소드는 `String.valueOf(...)` 입니다. 또는 직접 Literal을 대입하는 방식으로도 내부적으로 `intern()` 을 호출합니다. 이 메소드는 Constant pool내부에서 string literal을 찾아 등록하거나 레퍼런스를 가져오는 방식으로 동작합니다.







