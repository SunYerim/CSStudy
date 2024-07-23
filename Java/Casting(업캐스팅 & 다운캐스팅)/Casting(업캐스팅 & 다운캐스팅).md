# Casting(업캐스팅 & 다운캐스팅) 정리

### Casting(형 변환)이란?
> 데이터 타입을 다른 데이터 타입으로 변환하는 것 <br>
변환 될 데이터 타입의 정보를 가지고 있어야 변환이 가능

<br>

### 형 변환 예시
``` java
// 자식은 부모의 데이터 정보를 전부 가지고 있으므로, 변환이 가능
Parent p = new Child(); 

// 부모는 자식의 데이터 정보를 전부 가지지 않으므로, 변환이 불가능
Parent Child = new Parent(); 
```
<br>

### 캐스팅이 필요한 이유?



<hr> <br>

### 업 캐스팅
> 자식 클래스가 부모 클래스 타입으로 캐스팅되는 것 <br>
 
``` java
Parent p = new Child();
```
- 묵시적으로 형 변환이 가능하다.<br>
- 자식 클래스만의 멤버는 접근이 불가능하다. <br>
- 오버라이딩 된 메소드는 자식이 재정의한 메소드가 실행 됨 (동적 바인딩)

<br>

### 다운 캐스팅
> 부모 클래스가 자식 클래스 타입으로 캐스팅 되는 것 <br>

``` java
Parent p = new Child(); // 업 캐스팅
Child c = (Child) p; // 다운 캐스팅
```
- 명시적으로만 형 변환이 가능하다.<br>
- 업 캐스팅 된 자식 클래스를 복구하는 것이다.
- 업 캐스팅 되지 않은, 즉 원래 부모 클래스인 인스턴스는 다운 캐스팅 할  수 없다.

<br> 
<hr> <br>


### 예시 문제

``` java
class Parent{
	String name;

	public void print() {
		System.out.println("Parent");
	}
}

class Child extends Parent{
	int num;

	@Override
	public void print() {
		System.out.println("Child");
	}
}

Parent p = new Child(); 

p.num; // 1
p.print(); // 2
Child c = (Child) new Parent(); // 3
```
### 1, 2, 3의 실행 결과

<details>
<summary>정답</summary>

<!-- summary 아래 한칸 공백 두어야함 -->
> (1) 오류 발생 - 자식 클래스 고유의 속성, 메서드는 사용하지 못한다. <br> 
(2) Child - 동적 바인딩이 되어 자식이 오버라이드한 메서드가 호출된다. <br> 
(3) ClassCastException - 문법오류는 아니라 컴파일 단계에서 통과되지만, 런타임 단계에서 예외가 발생

</details>

<br>

## 예상 질문 : 업/다운 캐스팅이 필요한 이유


### 업 캐스팅
#### 1. `다형성 활용`
자바의 오버라이딩/ 오버로딩을 활용하여 `재사용성`을 높이고, `가독성`을 높일 수 있다.

#### 2. `코드의 유연성`
하위 클래스를 하나의 상위 클래스로 묶어 관리할 수 있다. 
``` java
Shape[] list = new Shape[10];
list[0] = new Circle();
list[1] = new Square();
list[2] = new Tran();
```

<br>

### 다운 캐스팅
#### 1. 자식 클래스의 `고유 기능` 사용
``` java
Parent p = new Child();
p.num; // 오류 발생, 자식 클래스 고유의 속성, 메서드는 사용하지 못한다.

Child c = (Child) p;
c.num; // 사용 가능
```
