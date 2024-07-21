# Thread 활용

### 멀티스레딩
> 하나의 프로세스 안에 여러 개의 스레드가 동시에 작업을 수행하는 것
<br>
<hr>

### 자바에서의 스레드 구현 방법


#### 1. Runnable 인터페이스를 구현하는 방법
``` java
class HelloThread2 implements Runnable {

	@Override
	public void run() {
		for (int i = 0; i < 100000; i++) {
			멀티스레드테스트.count++;
		}
	}
}
```

#### 2. Thread 클래스 상속
``` java
class HelloThread extends Thread {

	@Override
	public void run() {
		for (int i = 0; i < 100000; i++) {
			멀티스레드테스트.count++;
		}
	}
}
```
<br><hr>

### Thread 실행
> start() 메소드로 호출

run()메소드를 호출하면, 그냥 메소드를 호출한 것과 같다.<br>
start() 메소드를 호출해야 `문맥 교환(context switching)`이 이루어지며 멀티스레드처럼 동작한다.


``` java
Thread t = new HelloThread(); // Thread 상속으로 구현한 스레드
t.start(); //스레드 실행
t.run(); // Thread 클래스 안의 run() 메서드를 호출
```

<br>

<hr>

### 동기화가 없는 멀티스레드
``` java
public class 멀티스레드테스트 {

	static int count = 0;

	public static void main(String[] args) throws InterruptedException {

		HelloThread ht = new HelloThread();
		
        // t 스레드 실행
		Thread t = new Thread(ht);
		t.start(); 
        // t2 스레드 실행
		Thread t2 = new Thread(ht);
		t2.start();
		
        //스레드 종료까지 대기
		t.join(); 
		t2.join();
		
		System.out.println(멀티스레드테스트.count);
	}
}

```

![멀티스레드결과](./image.png)
> 동기화를 하지 않은 멀티 스레드는 잘못된 동작을 할 수 있음

<br>

### synchronized 키워드 사용
synchronized 키워드를 사용하면 임계 영역을 설정할 수 있다.<br>
임계영역 : `여러 쓰레드`가 공유 데이터에 접근하여 작업하는 코드 블록 <br>
쓰레드가 임계영역에 들어가면 lock을 얻게 되고, 해당 lock이 반환되기 전까지 다른 쓰레드가 접근할 수 없다.

``` java
class HelloThread extends Thread {

	@Override
	public void run() {
		for (int i = 0; i < 100_000; i++) {
			updateCount();
		}
	}
	
	public synchronized void updateCount() {
		멀티스레드테스트.count++;
	}
}
```
<br>

### wait()와 notify()
> 특정 스레드가 lock을 가진 상태로 오랜 시간을 보내지 않도록 해야 한다.
- wait() : 스레드가 lock을 반납하고 대기하게 만든다.
- notify() : 대기 상태인 스레드에게 lock을 부여한다.
이 메소드는 임계 영역 내에서 사용되어야 한다.

``` java
// 빵이 10개 이상인 경우 그만 생산
public synchronized void makeBread(){

    if (breadCount >= 10){
        try {
            wait(); // lock 반납
        } catch (Exception e) {}
    }

    // 빵이 10개 미만인 경우 생산
    breadCount++;
    notify();    // lock 얻을 수 있다면 얻기
}

// 빵이 1개 미만인 경우 그만 먹기
public synchronized void eatBread(){

    if (breadCount < 1){
        try {
            wait(); // lock 반납
        } catch (Exception e) {}
    }

    // 빵이 1개 이상인 경우 먹기
    breadCount--;
    notify(); // lock 얻을 수 있다면 얻기
}
```
> 스레드 간의 협력관계가 있는 경우, wait()와 notify()를 이용해서 더 효율적인 처리가 가능하다.

<hr> <br>

### Q. HashTable, HashMap, ConcurrentHashMap 중 멀티스레드에서 사용할 수 있는 것?
> HashTable 과 ConcurrentHashMap

### Q. HashTable과 ConcurrentHashMap의 차이?
### HashTable
메소드 단위의 Lock(synchronized)을 통해 `동기화 처리`는 가능하지만, `성능이 좋지 않다.` <br>
### ConcurrentHashMap
조회의 경우 Lock이 없으며, 메소드 단위가 아닌 버킷 단위의 Lock을 통해 `효율적으로 동기화 처리`가 가능하다.
