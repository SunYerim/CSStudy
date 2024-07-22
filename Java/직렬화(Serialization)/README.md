## 직렬화
자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 **바이트(byte) 형태로 데이터 변환**하는 기술

> 그 반대 개념인 **역직렬화**는 바이트로 변환된 데이터를 원래대로 자바 시스템의 Object 또는 Data로 변환하는 기술이다.

<br>

> 📢 ***바이트 스트림이란?*** <br>
스트림은 클라이언트나 서버 간에 출발지 목적지로 입출력하기 위한 데이터가 흐르는 통로를 말한다. <br><br> <u>자바는 스트림의 기본 단위를 **바이트**</u>로 두고 있기 때문에 네트워크, 데이터베이스로 전송하기 위해 **최소 단위인 바이트 스트림으로 변환하여 처리**한다.

<br>

<img src="https://github.com/user-attachments/assets/12fc7698-806b-405e-b2bd-4c0cb721a243">

<br><br>

- 각자 PC의 OS마다 서로 다른 가상 메모리 주소 공간을 갖기 때문에, Reference Type의 데이터들은 인스턴스를 전달 할 수 없다.
- 이런 문제를 해결하기 위해 주소값이 아닌 **Byte 형태로 직렬화된 객체 데이터를 전달**
- 직렬화된 데이터들은 모두 **Primitive Type(기본형)** 이 되고, 이는 파일 저장이나 네트워크 전송 시 파싱이 가능한 유의미한 데이터가 된다.

<br>

💡 ***<u>전송 및 저장이 가능한 데이터</u>로 만들어주는 것이 바로 직렬화!***

<br><br>

### 직렬화 조건
---
자바에서는 간단히 ***java.io.Serializable*** 인터페이스 구현으로 직렬화/역직렬화가 가능하다.

- 직렬화 대상 : 인터페이스 상속 받은 객체, Primitive 타입의 데이터

→ Primitive 타입이 아닌 Reference 타입처럼 주소값을 지닌 객체들은 바이트로 변환하기 위해 Serializable 인터페이스를 구현해야 한다.

<br>

### 직렬화 상황
---
1. **JVM에 상주하는 객체 데이터를 영속화할 때 사용** <br>
객체 데이터가 시스템이 종료되더라도 나중에 다시 재사용이 될 수 있을 때 영속화를 해두면 좋다

<br>

2. **Servlet Session** <br>
만일 세션 데이터를 저장 & 공유가 필요할 때 직렬화를 이용한다.

<br>

3. **Cache** <br>
데이터베이스로부터 조회한 객체 데이터를 다른 모듈에서도 필요할 때 재차 DB를 조회하는 것이 아닌 **객체를 직렬화하여 메모리나 외부 파일에 저장**해 두었다가 역직렬화하여 사용하는 캐시 데이터로서 이용이 가능하다.

<br>

4. **Java RMI(Remote Method Invocation)** <br>
원격 시스템 간의 메시지 교환을 위해서 사용하는 자바에서 지원하는 기술이다.이 메세지에 객체 데이터를 직렬화하여 송신하는 것이다.

<br>
<br>

### 직렬화 구현
---

```
@Entity
@AllArgsConstructor
public class Post implements Serializable {
    private static final long serialVersionUID = 1L;

    private String title;
    private String content;

    Post post = new Post("제목", "내용");
    byte[] serializedPost;
    try (BtyeArrayOutputStream baos = new ByteArrayOutputStream()) {
        try(ObjectOutputStream oos = new ObjectOutputStream(baos)) {
            oos.writeObject(post);

            serializedPost = baos.toByteArray();
        }
    }
}
```
👉 **ObjectOutputStream**으로 직렬화를 진행한다. Byte로 변환된 값을 저장하면 된다.

<br>

### 역직렬화 구현
---
```
try(ByteArrayInputStream bais = new ByteArrayInputStream(serializedPost)) {
    try(ObjectInputStream ois = new ObjectInputStream(bais)) {
        Objec objectPost = ois.readObject();
        Post post = (Post)objectPost;
    }
}
```
👉 **ObjectInputStream**으로 역직렬화를 진행한다. Byte의 값을 다시 객체로 저장하는 과정

<br>

> 📢 ***직렬화 serialVersionUID*** <br>
위 코드에서 serialVersionUID를 직접 설정했는데 사실 선언하지 않아도 자동으로 해시값이 할당된다 <br>그러나 **기존의 클래스 멤버 변수가 변경되면 serailVersionUID가 달라지는데** 역직렬화 시 달라진 넘버로 Exception이 발생될 수 있다<br>따라서 **직접 serialVersionUID를 관리**해야 클래스의 변수가 변경되더라도 직렬화에 문제가 없다.

<br>

💡 직렬화 데이터는 타입, 클래스 메타 정보를 포함하므로 사이즈가 크다. 트래픽에 따라 비용 증가 문제가 발생할 수 있기 때문에 **JSON 포맷으로 변경하는 것이 좋다**

<br><br>

### 면접 질문 예상
---
<br>

<details>
  <summary>직렬화와 역직렬화에 대해서 설명해주세요.</summary>
    <br>
    직렬화란 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터 변환하는 기술과 바이트로 변환된 데이터를 다시 변환하는 기술(역직렬화)을 아울러서 이야기 합니다.
    <br><br>
    자바 직렬화는 JVM의 메모리에서만 상주되어있는 객체 데이터를 영속화가 필요할 때 사용됩니다.
    <br><br>
    시스템이 종료되더라도 없어지지 않는 장점을 가지며 영속화된 데이터이기 때문에 네트워크로 전송이 가능합니다.
</details>


<br><br><br>

**[참고]**  https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0