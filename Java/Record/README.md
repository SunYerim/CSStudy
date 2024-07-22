

## Record

> Java 16부터 정식 기능으로 포함된 기능



## 기존 POJO의 문제점

* boilerplate code가 많다

* 데이터가 변경될 때 마다, `getter`, `setter`, `toString`, `equals` 를 추가하는 것은 비효율적, 실수 가능성 O

* `class 기반으로 선언`되었기 때문에, 순수한 데이터 그 자체를 표현하는 것이 모호하다.

  *  `데이터들을 표현할 수 있는 집합`을 표현할 수 있는 수단의 필요성이 증가함.

  

## 데이터의 집합을 표현할 수 있는 새로운 수단 `Record`

[참고](https://openjdk.org/jeps/359)

### 특징

* `getter계열, equals, hashCode, toString` 기능을 내부적으로 정의

* 불변 객체
  * 각 필드들은 private final로 선언된다.
    * 그 외, 선언될 수 있는 필드는 static이여야한다.
  * 기본적으로 record에서는 `모든 인자를 가진 public 생성자`가 존재
    * e.g.) lombok의  `@RequiredArgsConstructor` 
* `abstract` 사용 불가능
* 내부적으로 `final`로 선언됨
  * record를 상속(extends)하는 것이 불가능
    * 이는 Records가 내부적으로 `java.lang.Record` 클래스를 상속받기 때문
* `getter()` 계열의 함수는 `필드명()` 을 통해서 값을 가져올 수 있음
  * e.g.) `name 필드` $\rightarrow$ `obj.name()` 

* 생성자 관련

  * <u>권장</u>) private final 필드를 초기화하는 것 이상으로, validation과정 등을 수행하기 위해서는 `compact constructor`를 선언

    * ```java
      public record Person(String name, String address) {
          // compact constructor
      	public Person {
      		Objects.requireNonNull(name);
      		Objects.requireNonNull(address);
      	}
      }
      ```

  * 클래스와 마찬가지로 생성자를 추가할 수 있음

    * ```java
      public record Person(String name, String address) {
          public Person(String name) {
              this(name, "unknown");
          }
          public Person(String name, int addrCode) {
              this(name, String.valueOf(addrCode));
          }
      }
      ```



## 예상 면접 질문

* record는 정말로 immutable한가요?

$\rightarrow$ weakly immutable합니다. record의 필드로 reference type이 들어올 경우,  record가 기본으로 생성해주는 생성자에서는 `얕은 복사`가 일어나기 때문에 완전한 불변성을 위해서는 compact constructor를 사용하여 `깊은 복사`가 이루어지게끔 만들어야합니다.

```java
public record Person(List<String> nameList) {
    public Person {
        nameList = List.copyOf(nameList);
    }
}
```



* spring bean을 모두 record로 선언할 수 있나요?

  * 아니오. Spring에서 관리하는 일부 bean들은 `CGLIB` proxy기반으로 생성됩니다. CGLIB 프록시 객체를 생성하기 위해서  `상속` 을 내부적으로 사용하기 때문에, 이 경우에는 사용할 수 없습니다.
  * [참고](https://woooongs.tistory.com/99)

  

* lombok 또한 보일러플레이트 코드를 작성하는 것에 도움을 주는데, 이것과의 차이점
  * lombok이라는 외부 라이브러리에 종속되지 않은 프로그램 개발시에는 Record가 유용하게 사용될 수 있습니다. 