## 문자열 클래스

### 1. String Class
변경 불가능 → 할당된 메모리 공간이 변하지 않는다.<br>
> '+' 또는 concat 메서드를 통해 기존에 생성된 String 객체에 다른 문자열을 붙이면 ✨**새로운 String 객체를 만든 후**✨ 이 객체에 연결된 문자열을 저장하고, 그 객체를 참조!

<table>
    <tr>
        <td align="center">장점</td>
        <td>
             ① 불변하기 때문에 단순한 조회 연산에서는 타 클래스보다 빠르게 읽을 수 있다. <br>
             ② 불변하기 때문에 멀티쓰레드 환경에서 동기화를 신경 쓸 필요가 없다.
        </td>
    </tr>
    <tr>
        <td align="center">단점</td>
        <td>① 문자열 연산이 많이 일어나는 경우, 더이상 참조되지 않는 객체는 Garbage Collection에 의해 제거되어야 하기 때문에 성능이 좋지 않다. <br>
        ② 문자열 연산이 많아질 때 내부적으로 char 배열을 사용하고 계속해서 객체를 만드는 오버헤드가 발생하므로 성능이 좋지 않다.
        </td>
    </tr>
</table>

<br>

### 2. StringBuffer & StringBuilder
String class와 다르게 **변경 가능한 문자열을 만들어 준다**! <br><br>
💡 문자열 연산에 있어서 클래스를 <u>한 번만 만들고 연산이 필요할 때 **크기를 변경**해서</u> 문자열을 수정한다.

<br><br>
두 클래스가 제공하는 메서드는 서로 동일하다. 
> ***그렇다면 두 클래스의 차이점은?***  &nbsp;&nbsp;    ``` 동기화 여부! ```
<br><br>
**StringBuffer**는 각 메서드별로 동기화 키워드(Synchronized Keyword)를 지원하여 멀티쓰레드 환경에서 안전하다.
<br><br>
반면, **StringBuilder**는 동기화를 보장하지 않는다. 하지만 단일 쓰레드에서의 성능은 StringBuffer보다 뛰어나다. (연산 처리가 빠르다.)

<br>

- 멀티 쓰레드 환경이라면 값 동기화 보장을 위해 StringBuffer를, 단일 쓰레드 환경이라면 StringBuilder 사용

<br><br>

### Conclusion
---
단순히 성능만 놓고 본다면 연산이 많은 경우, ```StringBuilder > StringBuffer >>> String```
<br><br>
📢 JDK1.5 이후에는 컴파일 단계에서 String 객체를 사용하더라도 StringBuilder로 컴파일 되도록 변경되었다. 따라서 String 클래스를 활용해도 StringBuilder와 성능상으로 차이가 없어졌다. 하지만 반복 루프를 사용해서 문자열을 더할 때에는 객체를 계속 추가해야 한다는 사실은 변함이 없다.

<br><br>
### 면접 질문 예상
---
<details>
  <summary>String, StringBuilder, StringBuffer 차이</summary>
    <br>
    String은 불변이다. StringBuilder와 StringBuffer는 이런 String의 특징 때문에 사용하는 가변 타입이라고 볼 수 있다. 
    <br><br>
    StringBuilder와 StringBuffer는 Thread-safe 여부의 차이가 있다. StringBuilder는 동기화를 보장하지 않아 Thread-safe하지 않다. 따라서 멀티 쓰레드 환경에서 사용할 때는 StringBuffer를 사용한다.
</details>
