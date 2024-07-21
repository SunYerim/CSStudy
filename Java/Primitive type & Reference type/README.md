## Primitive type & Reference type
자바에는 기본형(Pribitive type)과 참조형(Reference type)이 있다.

<br>

### Primitive type (기본형 타입)
---
JAVA에서는 총 8가지의 Primitive type을 미리 정의하고 제공한다.

- 자바에서는 기본 자료형은 반드시 사용하기 전에 선언되어야 한다.

<u>스택 메모리</u>에 저장되며, **비객체** 타입이다. 따라서 null 값을 가질 수 없다. <br>
💡 ***만약에 Primitive type에 null을 넣고 싶다면 Wrapper Class를 활용한다.***

<br><br>

### 기본 자료형의 종류
---

- boolean 
  - ```논리형```인 boolean의 ```기본값은 false```이며 참과 거짓을 저장하는 타입
  - 주로 yes/no, on/off 등의 논리 구현에 주로 사용되며 ```두 가지 값만 표현하므로 가장 크기가 작다```
  - boolean은 실제로 1bit면 충분하지만 데이터를 다루는 최소 단위가 1byte이므로 ```메모리 크기가 1byte```이다.

- byte
    - 주로 이진데이터를 다루는데 사용되는 타입

- short
    - C언어와의 호환을 위해 사용되는 타입으로 잘 사용되지는 않는 타입

- int
    - 자바에서 ```정수 연산을 하기 위한 기본 타입```
    - byte 혹은 short의 변수가 연산을 하면 연산의 결과는 int 형이 된다.
    - ```메모리 크기는 4byte```

- long
    - 수치가 큰 데이터를 다루는 프로그램에서 주로 사용
    - long 타입의 변수를 초기화 할 때에는 ```정수값 뒤에 알파벳 L을``` 붙여서 long 타입(즉, 8byte)의 정수 데이터임을 알려주어야 한다.
    - 정수값이 int의 값의 저장 범위를 넘는 정수에서 L을 붙이지 않는다면 컴파일 에러 발생
    - ```메모리 크기는 8byte```
    - ex) ```long l = 2147483648L```
- float, double
    - 실수를 가수와 지수 형식으로 저장하는 ```부동소수점 방식```으로 저장
    - 가수를 표현하는데 있어 double형이 float형보다 표현 가능 범위가 더 크므로 ```double형이 보다 정밀하게 표현```할 수 있다.
    - ```자바에서 실수의 기본 타입은 double형```이므로 float형에는 알파벳 F를 붙여서 float형임을 명시해주어야 한다.

<br><br>

### Reference type (참조형  타입)
--- 
자바에서 Primitive type을 제외한 타입들이 모두 Reference type이다.

💡 ***Reference type은 JAVA에서 최상인 java.lang.Object 클래스를 상속하는 모든 클래스들을 말한다.***

→ new로 인하여 생성하는 것들은 메모리 영역인 ```Heap 영역```에 생성을 하게 되고, Garbage Collector가 돌면서 메모리를 해제한다

- 클래스 타입, 인터페이스 타입, 배열 타입, 열거 타입이 있다
- 빈 객체를 의미하는 null이 존재한다
- 문법상으로는 에러가 없지만 실행시켰을 때 에러가 나는 런타임 에러가 발생한다. ```(ex. 배열을 null 값으로 받으면 NullPointException이 발생)```
- Heap 메모리에 생성된 인스턴스는 메소드나 각종 인터페이스에서 접근하기 위해 JVM의 Stack 영역에 존재하는 Frame에 일종의 포인터(C의 포인터와 다름)인 참조값을 가지고 있어 이를 통해 인스턴스를 핸들링 
<br><br> <img src="https://github.com/user-attachments/assets/c98d427d-1338-4315-af52-2065ca3ad3d7" />

<br><br>

### String Class
---
참조형에 속하지만 기본적인 사용은 기본형처럼 사용한다. 

- ⭐***불변하는 객체*** ⭐
    - String 클래스에는 값을 변경해주는 메소드들이 존재하지만 해당 메소드를 통해 데이터를 바꾼다 해도 **새로운 String 클래스 객체를 만들어내는 것!**
    - 일반적으로 기본형 비교는 == 연산자를 사용하지만 String 객체간의 비교는 .equals() 메소드를 사용 
    
    <br>

    > 💡 ***주소값 비교(==)와 값 비교(equals)*** <br><br> == 연산자와 equals() 메소드의 가장 큰 차이점은 == 연산자는 비교하고자 하는 두 개의 대상의 **주소값을 비교**하는데 반해 String 클래스의 equals 메소드는 비교하고자 하는 두 개의 대상의 **값 자체**를 비교한다는 것이다. <br><br> 기본 타입의 int, char형 등은 Call by Value 형태로 기본적으로 대상에 주소값을 가지지 않는 형태로 사용된다. 하지만 String은 일반적인 타입이 아니라 클래스이다. 클래스는 기본적으로 Call by Reference 형태로 생성 시 주소 값이 부요된다. 그래서 **String 타입을 선언했을 때는 같은 값을 부여하더라도 주소값이 다르다.**

    <br><br>

### 면접 질문 예상
---
<details>
    <summary>자바의 원시타입들은 무엇이 있으며 각각 몇 바이트를 차지하나요?</summary>
        <br>
        boolean(1), char(unsigned 2), byte(1), short(2), int(4), long(8), float(4), double(8)
</details>

<br>

<details>
    <summary>원시타입과 참조타입의 차이에 대해 설명해주세요.</summary>
        <br>
        원시타입은 java에서 단 8개 밖에 존재하지 않는 타입입니다. 나머지는 모두 참조 타입으로 볼 수 있고, Object 타입이거나 이를 상속하는 클래스들로 이루어져 있습니다.
        <br><br>
        원시타입은 항상 값이 존재해야 하지만 참조타입은 null을 가질 수 있습니다. 그리고 원시타입은 멤버 변수가 초기화될 때 기본값을 가지지만 참조타입은 null 포인터를 가집니다.
</details>