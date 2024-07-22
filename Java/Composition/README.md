

# Composition

상속은 `코드를 재사용`한다는 점에서 유용하다. 하지만, 잘못사용할 경우 오류를 내기 쉬운 소프트웨어를 만들게 된다.

## 상속을 잘못사용하는 경우

* **일반적인 구체 클래스를 package-private의 경계를 넘어서 다른 패키지의 구체 클래스를 상속받는 경우**

이 경우, 상위 클래스를 어떻게 구현하느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다.(캡슐화 원칙을 깨뜨린다) 

만약, 상위클래스에서 확장을 고려하지 않고 문서화 또한 적절히 이루어져있지 않다면 하위 클래스의 코드를 손대지 않더라도 하위 클래스가 오동작할 수 있다. 



ex) 상속을 잘못사용한 예

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;
    public InstrumentedHashSet() {}
   	public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }

    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }
}
// Belows are Driver Program
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("강", "민", "준"));
s.getAddCount(); // 결과: 6 (왜? 3이 아닐까)
```

Q. `s.getAddCount()` 의 결과는 왜 3이 아닌 6이 나올까?

A. 상위클래스의 `HashSet`이 `addAll()` 을 수행할 때, 내부적으로 `add` 를 호출하기 때문. (이러한 이유를 알기 위해서는 상위클래스에서 내부 동작을 기술해야함. $\rightarrow$ 캡슐화 원칙 훼손)
```java
// 간단하게 HashSet 동작 방식을 기술
public class HashSet<E> {
	public boolean add(E c) {
		return map.put(e, PRESENT) == null;
    }
    public boolean addAll(E e) {
        Iterator<> iter = e.iterator();
        while (iter.hasNext()) {
            add(iter.next());
        }
    }
}
```



![incorrectInheritence](https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/incorrectInheritence.png)

---

구체클래스의 상속은 상위 클래스의 메소드들을 재정의할 수 있으므로 하위 클래스에서 예기치 못한 오류를 발생시킬 수 있다.

$\rightarrow$ 상위 클래스의 구현 spec이 달라지면(ex.메소드 추가) 그에 맞추어 하위 클래스도 대응해주어야한다는 문제점이 존재.



## 대부분의 경우 상속보다 더 나은 방법이 존재한다.

기존의 클래스를 확장하는 것 대신, 새로운 클래스의 private 필드로 기존 클래스의 인스턴스를 참조하게 하자.

(기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻으로 이것을 **컴포지션** 이라고 한다.)

```java
public class Store {
    private final CashManager cashManager; // DI 받을 예정
    private final MemberManager memberManager; // DI 받을 예정
    
}
public class Store extends Manager {
    
}
```



* **전달(forwarding)**

	새 클래스의 인스턴스 메서드들은 <u>전달 메서드</u>를 호출하여 그 결과를 반환한다.

* **전달 메서드(forwarding method)**

	기존 클래스의 대응하는 메서드를 호출하여 그 결과를 반환하는 메서드.



컴포지션은 상속의 문제점인 `내부 구현 종속성`을 제거하였다. 그 결과로 아래와 같은 이점을 얻게 되었다.

* 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어남. (단순하게, 결과값을 요청하고 그 결과를 받아온다)

	이러한 **컴포지션 + 전달 방식**을 위임(delegation)이라 한다.

![composition](https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/composition.png)

---

**코드의 재사용을 위하여 기존 클래스의 부가기능을 추가하는 상황에서는 컴포지션이 권장된다. (데코레이터 패턴)**

데코레이터 패턴을 이용하여 래퍼 클래스를 설계해보자.

```java
public class Integer {
    private final int value;
    public String toString() { /* ... 생략 ... */ }
    public Character[] toCharArray() {/* 생략 ... */}
}
```

위와 같이, `Integer`를 표현하는 핵심요소인(`value`)와 부가기능(`toString()`, `toCharArray`) 을 추가한 새로운 클래스를 생성한 것과 동일한 효과를 낼 수 있다.

* 이러한 래퍼 클래스는 콜백 프레임워크와는 어울리지 않는다는 점만 제외한다면, 단점이 거의 없다.

![wrapper](https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/wrapper.png)

A가 `notify`를 호출할 때, callback을 위하여 자신의 레퍼런스(this)를 `notify`에게 넘겨주게된다.

Callee인 EventListener는 해당 레퍼런스로 호출할 객체를 찾게되는데, 이 경우에 Wrapper의 존재를 모른다.

$\rightarrow$ SELF문제



---

## 그렇다면 언제 상속을 쓰고 언제 컴포지션을 사용하는가?

상속은 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 쓰여야한다. (`is -a` 관계)

그렇지 않다면, 대부분 컴포지션으로 해결이 가능한 문제이다. 즉 필수 구성요소가 아닌 선택 구성요소로서 구현이 가능하다.

(`has -a` 관계)



**만약, 상속과 컴포지션 둘 다 사용가능한 상황이라면 컴포지션을 선호하자.**

* 상속은 내부 구현을 불필요하게 노출한다. 그 결과로, API가 내부 구현에 묶이고 클래스의 성능 개선도 어려워진다.
* <u>클라이언트에서 상위 클래스를 직접 수정하여 하위 클래스의 불변식을 해칠 수도 있다.</u>(클라이언트가 노출된 내부에 직접 접근할 수 있다)



## 상속을 사용하기 전 마지막으로 고려해야할 점

* 확장하려는 클래스의 API에 아무런 결함이 없는가?

	만약 결함이 있다면, 하위 클래스의 API까지 승계된다. 

* 컴포지션 같은 경우, 추후에 이러한 결함을 숨기는 새로운 API를 설계할 수 있다.





## Summary

* 상속은 강력하지만 캡슐화 원칙을 해친다.
* 상속은 `is -a`관계일때만 사용하자. 이 경우에는 반드시 상위 클래스가 확장을 고려하여 설계되고 내부 구현에 대한 적절한 문서화가 필요하다.
* 상속의 취약점을 피하려면, 최대한 컴포지션 + 전달의 조합을 사용하자. (Delegation)
* 래퍼 클래스는 하위 클래스보다 견고하고 강력하다. (확장이 편리하다)





## 면접 예상 질문

1. 컴포지션은 어떤 경우에 사용하나요?
2. 그렇다면 상속과의 차이점은 무엇인가요?