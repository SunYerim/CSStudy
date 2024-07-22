## Stream API

> Java 8 이후 등장



## prerequisite

* 함수형 프로그래밍 패러다임의 등장
* 자바에서는 이를 지원하기 위해서 `함수형 인터페이스` 가 등장
* 이를 파라미터로 받아서, 집계를 위한 연산들의 pipeline 구성 가능



## Stream?
"A Sequence of elements supporting sequential and parallel aggregate operations" 

위는 `Stream`의 공식 문서 설명이다.
스트림은 설명대로, 데이터를 순차적 또는 병렬적으로 집계하여 처리할 수 있는 기능들을 제공하는 라이브러리이다. (병렬처리 -> [spliterator 이용](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html))

이전까지는 데이터를 집계하기 위해서 `for`문 기반의 집계 로직을 설계하였다. 시간이 흐름에 따라, 데이터의 복잡성이 높아지고 처리해야할 로직이 증가하면서 반복문 기반의 데이터 집계 방식은 **가독성**이 많이 떨어졌는데, 이러한 문제점을 해결해주기 위해 `stream`이 등장했다고 해석하면 될 것 같다.

---

## 특징

* **데이터를 저장하기 위한 공간이 아니다.**
(source로부터 온 데이터를 computational pipe로 넘기는 과정)

* **Functional in nature** 
stream을 통해 들어온 데이터가 수정되더라도, source의 데이터는 변경되지 않는다 (= Side effect가 없다)

* Stream Operation은 **`Intermediate`, `Terminate` 으로 구분된다**.
`Intermediate` : 스트림을 반환하는 Operation $\rightarrow$ *또 다른 Intermediate operation으로 체이닝이 가능하다.*
`Terminate` : 스트림이 아닌 타입을 반환하는 Operation
최종 스트림에서 데이터를 집계하는 역할을 담당한다.

* `Intermediate Operation`은 **Lazy하게 동작**한다.
성능상의 이점을 위해서, Stream의 결과는 `Terminal Operation`이 등장할 때 평가한다.

* **Possibly unbounded** 
컬렉션 타입과 달리, 스트림을 지나가는 데이터의 개수가 무제한이다. (`Short-Circuit`을 통해서 제한을 걸수도 있다)

* **Consumable**
스트림으로 처리하는 데이터는 단 한번만 처리한다. 즉, 한번 지나갔던 데이터를 다시 참조할 수 없다 (단방향 `Iterator`와 유사하다)

---

## Stream Pipeline
Stream에서 여러 개의`Intermediate Operation`과 하나의 `Terminate Operation`으로 최종 결과를 받아올 때, Stream의 Operation 집합을 Stream pipeline이라고 한다.

Consumable하고 `Intermediate Operation`의 결과가 다른 `Intermediate Operation`의 input으로 들어온다는 사실에 매우 적절한 이름이라고 생각한다!

---

## Stream API
Stream API 중 가장 많이 쓰이는 몇 개만 선정하여 기술하겠다.


* `Collection.stream()` 
sequential and parallel 처리를 위해 자바의 컬렉션 클래스들은 모두 스트림을 생성하는 팩토리가 존재한다.

**중계형 오퍼레이션**

1. `Stream.map(Function<T, R> mapper)` 
T는 스트림의 타입이고, R은 매핑되는 결과에 대한 타입 파라미터.
Stream내부에 존재하는 원소들 각각에 대해서, R이라는 결과로 매핑 시켜주는 역할을 수행한다. 
![](https://velog.velcdn.com/images/joonamin44/post/7baecf6e-f53f-4d5e-a4cb-8b8a1fe5ca74/image.png)


```java
List<String> names = new ArrayList<String>(Arrays.asList("a","b","c","D"));

names.stream()
		.map(s -> s.toUpperCase())
        .forEach(System.out::println);
```

2. `Stream.filter(Predicate<T> predicate)`
조건식을 만족하는 (= predicate에 대해 `true`) 원소들만 Stream에 포함시킨다.
![](https://velog.velcdn.com/images/joonamin44/post/cc48f6ba-c9d0-420f-96ce-816caf2f3c0e/image.png)

```java
List<String> names = new ArrayList<String>(Arrays.asList("a","b","c","D"));

names.stream()
		.filter(s -> {
        	for (char c : s.toCharArray()) {
            	if (Character.isUpperCase(c))
                	return false;
            }
            return true;
        })
        .forEach(System.out::println);
```


3. `Stream.flatMap(Function<T, R> mapper)`
컬렉션을 이용하여 stream을 만들 경우, 컬렉션의 원소 타입을 Stream이 wrapping한 형태로 새로운 스트림이 만들어진다. 
ex) `List<List<String>>` $\rightarrow$ `Stream<List<String>>` 
`flatMap`은 이런 중첩 구조를 flatten해준다.
![](https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/image.jpg)
**Stream의 각 값을 다른 스트림으로 만든 다음, 모든 Stream들을 하나의 Stream으로 반환한다.**
위의 예시를 살펴보자.
먼저, `map()`으로 생성된 stream은 `String[]`의 형식을 가진다. 
이 후, `flatMap`을 호출하면 `String[]`의 각각의 값(`String`)에 대해서 stream을 만든 다음, 이것을 하나의 스트림 (`Stream<String>`)으로 만들어 주는 것을 확인해 볼 수 있다.
```java
List<String> names1 = new ArrayList<String>(Arrays.asList("a","b","c","D"));

List<String> names2 = new ArrayList<String>(Arrays.asList("A","B","C","d"));

List<List<String>> nameTable = new ArrayList<>();
nameTable.add(names1);
nameTable.add(names2);

List<String> collect = nameTable.stream()
	.flatMap(Collection::stream)
    .collect(Collectors.toList());
```

---

**종료형 오퍼레이션**
집계 처리를 위한 종료형 오퍼레이션이다.
종료형 오퍼레이션을 수행할 때, 중계형 오퍼레이션에 대한 <u>순차적</u>으로 수행하고 종료형 오퍼레이션을 수행하면 집계된 결과를 얻을 수 있다.

1. `collect(Collector<?>)`
결과 Stream을 적절히 처리하여 결과 값으로 파싱해준다.
`Collectors` 클래스에서 원하는 결과 컬렉션으로 변환해주는 정적 팩토리를 지원한다. 
ex) `Collectors.toList(), Collectors.toSet()`

---
2. `forEach(Consumer<>)`
결과 Stream에 존재하는 원소들 각각에 대한 행위를 기술하고, 기술된 행위를 수행한다.

---
3. `anyMatch(Predicate<>)`, `allMatch(Predicate<>)`, `noneMatch(Predicate<>)` 
결과 Stream에 존재하는 원소들 중, 조건식을 만족하는 원소의 개수에 따라서 boolean을 리턴한다.

---
4. `count()`
결과 Stream에 존재하는 원소들의 개수를 리턴한다.