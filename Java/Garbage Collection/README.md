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
 ② 오래된 객체에서 최신 객체로의 참조는 아주 적게 존재한다.


<br><br>

### Garbage Collection의 메모리 해제 과정
---
1. Marking
- 프로세스는 마킹을 호출한다. 이것은 GC가 ```메모리가 사용되는지 아닌지를 찾아낸다.``` 모든 오브젝트는 마킹 단계에서 결정을 위해 스캔되어 진다. 모든 오브젝트를 스캔하기 때문에 매우 많은 시간을 소모하게 된다.

2. Normal Deletion
- ```참조되지 않는 객체를 제거하고 메모리를 반환한다.``` 메모리 Allocator는 반환되어 비어진 블럭의 참조 위치를 저장해 두었다가 새로운 오브젝트가 선언되면 할당되도록 한다.

3. Compacting
- 참조되지 않는 객체를 제거하고 ```남은 참조되어지는 객체들을 묶는다.``` 이들을 묶음으로서 공간이 생기므로 새로운 메모리 할당 시 더 쉽고 빠르게 진행 할 수 있다.