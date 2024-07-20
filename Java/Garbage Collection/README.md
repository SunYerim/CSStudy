## Garbage Collection
JAVA에서는 JVM이 구성된 JRE가 제공되며, 그 구성 요소 중 하나인 Garbage Collection이 메모리 누수를 막기 위해 자동으로 사용하지 않는 객체를 파괴한다. <br><br>
💡 ***자바는 개발자가 명시적으로 객체를 해제할 필요가 없다***

<br>

> ***'stop-the-world'*** <br>
Garbage Collection을 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다. <br><br>
📢 어떤 Garbage Collection 알고리즘을 사용하더라도 'stop-the-world'는 발생하게 되는데 보통 Garbage Collection 튜닝은 이 'stop-the-world' 시간을 줄이는 것!

<br>

- Garbage Collection을 해도 더이상 사용 가능한 메모리 영역이 없는데 계속 메모리를 할당하려고 하면 ```OutOfMemoryError```가 발생하여 WAS가 다운될 수도 있다

<br><br>

### Garbage Collection의 대상이 되는 경우
---

기본적으로 JVM의 메모리는 class, stack, heap, native method, PC 이렇게 총 5가지 영역으로 나뉘는데 Garbage Collection은 ```힙 메모리```만 다룬다.

<br>

1. 객체가 NULL인 경우
2. 블럭 실행 종료 후, 블럭 안에서 생성된 객체
3. 부모 객체가 NULL인 경우, 포함하는 자식 객체

<br>

Garbage Collection은 ```Weak Generational Hypothesis```에 기반한다. 

> ***Weak Generational Hypothesis*** <br>
 Garbage Collection은 두 가지 가정 하에 만들어졌다. <br><br>
 ① 대부분의 객체는 금방 접근 불가능 상태가 된다 <br>
 ② 오래된 객체에서 신규 객체로의 참조는 아주 적게 존재한다.
 <br><br>
 → 이 가설에 기반하여 자바는 young 영역과 old 영역으로 메모리를 분할하고, <u>신규로 생성되는 객체는 young 영역</u>에 보관하고 <u>오랫동안 살아남은 객체는 old 영역</u>에 보관한다.


<br><br>

### Garbage Collection의 메모리 해제 과정
---
1. Marking
- 프로세스는 마킹을 호출한다. 이것은 GC가 ```메모리가 사용되는지 아닌지를 찾아낸다.``` 모든 오브젝트는 마킹 단계에서 결정을 위해 스캔되어 진다. 모든 오브젝트를 스캔하기 때문에 매우 많은 시간을 소모하게 된다.

2. Normal Deletion
- ```참조되지 않는 객체를 제거하고 메모리를 반환한다.``` 메모리 Allocator는 반환되어 비어진 블럭의 참조 위치를 저장해 두었다가 새로운 오브젝트가 선언되면 할당되도록 한다.

3. Compacting
- 참조되지 않는 객체를 제거하고 ```남은 참조되어지는 객체들을 묶는다.``` 이들을 묶음으로서 공간이 생기므로 새로운 메모리 할당 시 더 쉽고 빠르게 진행 할 수 있다.


### Generational Gabage Collection
---
1. young 영역 <br>
새롭게 생겅한 객체의 대부분이 여기에 위치한다. 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 young 영역에 생성되었다가 사라진다. 
이 영역에서 객체가 사라질 때 **Minor GC**가 발생한다고 말한다.

<br>

2. old 영역 <br>
접근 불가능 상태로 되지 않아 young 영역에서 살아남은 객체가 여기로 복사된다. 대부분 young 영역보다 크게 할당하며, 크기가 큰 만큼 young 영역보다 gc는 적게 발생한다. 이 영역에서 객체가 사라질 때 **major GC(또는 full GC)** 가 발생한다고 말한다.

<br>

3. permanet 영역 <br>
method area라고도 한다. JVM이 클래스들과 메소드들을 설명하기 위해 필요한 메타데이터들을 포함하고 있다. <br>
``` JDK8부터는 PermGen은 metaspace로 교체된다. ```

<br>

> ***Permanent Generation 영역이란?***<br>
PermGen 영역은 heap 영역에 속하며, 현재까지 로드한 클래스와 메서드의 메타 데이터, 정적 변수와 상수 정보들이 저장되는 공간이다. <br><br>
***Metaspace 영역이란?*** <br>
현재까지 로드한 클래스와 메서드의 메타 데이터가 저장되는 공간이다. heap 영역이 아니라 native 메모리 영역에 위치한다.
<br><br>
💡 ***PermGen이 Metaspace 영역으로 바뀐 이유*** <br>
PermGen은 제한된 메모리 영역이기 때문에 OutOfMemoryError가 발생한다. 이 문제를 해결하기 위해 Native 메모리를 사용하는 Metaspace로 바뀌게 된 것이다.

<br><br>
### Generational Grabage Collection 과정
---
1. 어떠한 새로운 객체가 들어오면 ```Eden Space```에 할당된다. <br><br>
💡 ***Eden Space&nbsp;:&nbsp;자바 객체가 생성되자마자 저장되는 곳***

<br>

2. Eden space가 가득차게 되면, ```minor garbage collection```이 시작된다.

<br>

3. ```참조되는 객체들은 첫 번째 survivor(S0)```로 이동되어지고, 비 참조 객체는 Eden space가 clear될 때 반환된다.

<br>

4. 다음 minor GC 때, Eden space에서는 같은 일이 일어난다. 비 참조 객체는 삭제되고 참조 객체는 survivor space로 이동하는 것이다. 그러나 이 케이스에서 ```참조 객체는 두 번째 survivor space로 이동```하게 된다. 게다가 최근 minor GC에서 **첫 번째 survivor space로 이동된 객체**들도 ```age가 증가하고 S1 공간으로 이동```하게 된다. 한 번 모든 surviving 객체들이 S1으로 이동하게 되면 S0와 Eden 공간은 Clear 된다. <br><br>
<img src="https://github.com/user-attachments/assets/744c7db8-078e-4260-a4b0-fe12eacec2c6" height=200>

<br>

5. 다음 minor GC 때, 같은 과정이 반복된다. 그러나 이번에 survivor space들은 switch된다. ```참조되는 객체들은 S0로 이동```한다. ```살아남은 객체들은 age가 증가```하고, Eden과 S1 공간은 clear 된다.

<br>

6. minor GC 후 aged 오브젝트들이 **일정한 age threshold을 넘게 되면** ```young generation에서 ole promotion``` 되어진다. 

<br>

7. minor GC가 계속되고 계속해서 객체들이 Old Generation으로 이동한다. 

<br>

8. 결국 major GC가 old Generation에 시행되고, old Generation은 Clear되고 공간이 Compact 되어진다. <br><br>
<img src="https://github.com/user-attachments/assets/3046fbda-33a6-4b9b-91ec-54934f55480c" height=200>

<br><br>
### 면접 질문 예상
---
<details>
  <summary>GC가 무엇인지, 필요한 이유는 무엇인가요?</summary>
    <br>
    GC는 힙 영역에서 사용하지 않는 객체들을 제거하는 작업을 말합니다. 
    <br><br>
    객체를 제거하는 작업이 필요한 이유는 자바는 개발자가 메모리를 직접 해제해줄 수 없는 언어이기 때문에 객체를 사용하고 제거하는 기능이 필요하게 된다.
</details>
<br>
<details>
  <summary>GC의 동작 방식을 설명해주세요.</summary>
    <br>
    GC는 Minor GC, Major GC로 구분할 수 있다. Minor GC는 young 영역에서 Major GC는 old 영역에서 일어난다고 정의한다. 
    <br><br>
    GC를 수행할 때 GC를 수행하는 스레드 이외의 스레드를 모두 정지하는데 이를 Stop-the-world라고 한다.
    <br><br>
    Minor GC는 Eden 영역이 가득 참에서 부터 시작한다. Eden 영역에서 참조가 남아있는 객체를 mark하고 survivor 영역으로 복사한다. 그리고 Eden 영역을 비운다. survivor영역도 가득차면 같은 방식으로 다른 survivor 영역에 복사하고 비운다. 이를 반복하다 보면 계속해서 살아남은 객체는 old 영역으로 이동하게 된다.
    <br><br>
    Major GC는 old 영역에서 일어난다. 위와 반대로 삭제되어야 하는 객체를 mark하고 지운다(sweep).
    <br><br>
    메모리는 단편화된 상태이므로 이를 한군데에 모아주는 것을 Compaction이라 하며 compact라고 한다. 그래서 Mark-Sweep-Compact 알고리즘이라고 한다.
    <br><br>
    GC 수행 시 시스템이 멈추기 때문에 의도치 않은 장애의 원인이 될 수 있다. 따라서 이를 위해 힙 영역을 조정하는 것을 GC 튜닝이라고 하고 JVM 메모리는 절대 마음대로 조정하면 안된다.
</details>
