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
> * 

1. ***Checked Exception***

   * compile time exception으로 부르기도 함

   * 명시적으로 예외 처리가 필요함

     

2. ***unchecked Exception***

   * 

