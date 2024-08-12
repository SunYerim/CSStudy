## IPC

프로세스는 독립적으로 실행된다. 즉, 다른 프로세스에게 영향을 받지 않는다고 말할 수 있다. 이런 **독립적 구조를 가진 프로세스 간의 통신**을 해야 하는 상황이 있는데 이를 가능하도록 해주는 것이 바로 IPC 통신이다.

<br>

> 💡 **_커널이란?_** <br>
> 운영체제의 핵심적인 부분으로, 다른 모든 부분에 여러 기본적인 서비스를 제공해준다.

<br>

### IPC 종류

---

1. **익명 PIPE** <br>
   파이프는 두 개의 프로세스를 연결하는데 하나의 프로세스는 데이터를 쓰기만 하고, 다른 하나는 데이터를 읽기만 할 수 있다. <br>
   **한쪽 방향으로만 통신이 가능한 <u>반이중 통신</u>** 이라고도 부른다 <br>
   따라서 양쪽으로 모두 송/수신을 하고 싶으면 2개의 파이프를 만들어야 한다 <br><br>
   매우 간단하게 사용할 수 있는 장점이 있고, 단순한 데이터 흐름을 가질 땐 파이프를 사용하는 것이 효율적이다. 단점으로는 전이중 통신을 위해 2개를 만들어야 할 때는 구현이 복잡해지게 된다

<br>

2. **Named PIPE(FIFO)** <br>
   익명 파이프는 통신할 프로세스를 명확히 알 수 있는 경우에만 사용한다. <br><br>
   Named 파이프는 **전혀 모르는 상태의 프로세스들 사이 통신**에 사용한다. <br>
   즉, 익명 파이프의 확장된 상태로 부모 프로세스와 무관한 다른 프로세스도 통신이 가능한 것 <br><br>
   하지만 Named 파이프 역시 읽기/스기 동시에 불가능! 따라서 전이중 통신을 위해 익명 파이프처럼 2개 만들어야 한다