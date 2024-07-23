## Error & Exception

### 개념

* 자바에서는 오류를 Error와 Exception으로 구분

  * **Error** 는 `수습할 수 없는 심각한 문제` 를 의미
    * e.g.) `OutOfMemoryError`
  * **Exception**은 미리 예측하여 방지할 수 있는 오류를 의미
    * 예외 처리가 필요하다.

* ```mermaid
  graph LR;
  A[Throwable]
  B[Exception]
  C[Error]
  O[Object]
  
  C --> A
  B --> A
  A --> O
  
  ```

* Throwable에는 오류의 원인을 알려주는 `getMessage()`와  스택 트레이스를 보여주는 `printStackTrace()` 메소드가 정의되어있음

---

### Exception

미리 예측하여 방지할 수 있는 오류에 집중하는 것이 적절하다.

Exception은 크게 2가지 타입으로 분류된다.

> 이건 왜 일까?
>
> * 컴파일 타임에 예외가 발생한다는 것이 아닌, 컴파일 타임에 **예측** 할 수 있고, 이에 대한 적절한 예외처리가 필요할 경우
>
>   * 이럴 경우, 명시적으로 어떠한 Exception을 Throws하는 지 상위 호출 메소드에 기재하는 것이 필수
>   * 또는, 적절하게 try-catch 로 예외처리가 필요함
>
> * 컴파일 타임에 예측할 수 없는 예외들일 경우 unchecked exception으로 지정. 명시적으로 Exception Handling이 필요없음
>
>   * > 클라이언트가 예외로부터 복구될 수 있다고 예상할수 있는 경우 Checked Exception을 선택합니다. 반면 클라이언트가 예외로부터 복구될 수 없는 경우 UnChecked Exception으로 지정합니다.
>
>     예를 들어, 파일을 열기전에 우리는 열고자 하는 파일의 이름을 Validation할 수 있습니다.
>
>     만약 유저가 입력한 파일이름이 존재하지 않는다면 우리는 custom checked exception을 발생시킬 수 있습니다.

[why you should avoid using checked exception](https://developer.vonage.com/en/blog/why-you-should-avoid-using-checked-exceptions-in-java)

[when to choose checked and unchecked exception](https://stackoverflow.com/questions/27578/when-to-choose-checked-and-unchecked-exceptions)



1. ***Checked Exception***

   * compile time exception으로 부르기도 함

   * 명시적으로 예외 처리가 필요함

     

2. ***unchecked Exception***

   * run time exception으로 부르기도 함
   * sneaky throws가 가능함





## 면접 예상 질문

* Error 와 Exception의 차이점을 알려주세요
  * Error는 수습할 수 없는 심각한 문제를 의미하며, 개발자가 미리 예측하여 방지할 수 없는 상태를 의미합니다. Exception은 개발자가 구현한 로직에서 발생한 논리적 오류나 사용자의 영향에 의하여 발생하는 것입니다. 

* Java의 exception에는 2가지 유형이 있는데, 이것에 대해서 설명해주세요
  * Java에서는 컴파일 타임에 예측이 가능하고 (and)  이를 복구 가능할 경우에는 `checked exception` 을 사용합니다. 그 외의 exception 타입은 `unchecked exception` 을 사용합니다.
