# 고유 락 (Intrinsic Lock)

### 고유 락 (Intrinsic lock)
자바의 모든 객체는 lock을 가지고 있다. <br>
`monitor lock` 혹은 `monitor`라고도 한다. <br>

### synchronized 블록은 `고유 락`을 이용해서 스레드의 접근을 제어한다.
```java
public class Counter{
    private Object lock = new Object(); // 모든 객체가 가능 (Lock이 있음)
    private int count;
    
    public int increase() {
        // 단계 1, lock이라는 객체의 고유 락을 이용하여 count 접근을 막는다.
        synchronized(lock){	
            return ++count;
        }
        
        // 단계 2, lock이라는 객체 말고 자신을 이용
        synchronized(this) { // this도 객체이므로 lock으로 사용 가능
        	return ++count;
        }
        
    }
    // 단계 3, 단계 2를 더 편하게 사용 가능
    public synchronized int increase() {
    	return ++count;
    }
    
}
```

### Reentrancy
- 자바의 고유 락은 `재진입이 가능`하다.
- 락의 획득이 `호출 단위`가 아닌 `스레드 단위`로 일어난다는 것을 의미
- 이미 락을 획득한 스레드는 같은 락을 얻기 위해 대기할 필요가 없음.
``` java
public class Reentrancy {
    // a 진입 시 lock 획득
    // b 진입 시 필요한 lock이 이미 있음, 진입 가능
    public synchronized void a() {
        System.out.println("a");
        b();
    }
    
    public synchronized void b() {
        System.out.println("b");
    }
    
    public static void main (String[] args) {
        new Reentrancy().a();
    }
}
```
> 재진입이 불가능하다면 a 메서드에서 b를 호출하는 지점에서 데드락 발생 가능

### Structured lock
- 고유 락을 이용한 동기화를 `구조적인 락(Structured lock)`이라고 함
- synchronized 블록 단위로 lock의 획득/해제가 일어난다.
A 획득 -> B 획득 -> B 해제 -> A 해제는 가능하지만, <br>
A 획득 -> B 획득 -> A 해제 -> B 해제는 불가능하다. <br>
이와 같은 순서로 락을 사용해야 한다면 RenntrantLock과 같은 `명시적인 락`을 사용해야 한다.

### Visibility(가시성)
- 하나의 스레드가 쓴 값을 다른 쓰레드가 볼 수 있는가? (볼 수 있어야 함)
- Structed Lock과 Reentrant Lock은 `가시성`을 보장한다.


### Q. 자바에서 고유 락이란?
자바에서 고유 락은 뮤텍스라고도 불리며, 멀티스레드 환경에서 하나의 스레드만이 접근하도록 하는 동기화 매커니즘이다.<br>
한 스레드가 고유 락을 획득하면, 다른 스레드는 그 락을 얻을 때 까지 대기해야 한다.

