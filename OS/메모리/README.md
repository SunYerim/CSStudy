# 메모리



## Prerequisite

* 메모리는 多 바이트로 구성되고, 각각의 바이트에는 고유한 주소가 할당된다.
* 하나의 process가 실행될 때, CPU는 PC(Program Counter)에 할당된 주소로 가서 명령어를 추출
  * 명령어를 decode 한 다음, operands를 레지스터나 메모리에서 추출
  * 실행 이후, 결과 값을 레지스터나 메모리에 load
* 현대 컴퓨터 구조인, 폰 노이만 구조에서는 CPU가 특정 명령어를 수행할 때 아래의 과정을 필수적으로 거침
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/download.jpg" alt="download" style="zoom:80%;" />





## 가상 메모리?

* 다수의 프로세스가 메모리에 할당될 수 있으므로, 메모리 관리의 어려움 발생 ➡️ <u>멀티 태스킹</u>
* 각각의 프로세스가 사용할 수 있는 `논리적`인 메모리 영역으로 구분
  * 이 후, MMU(Memory Management Unit) 가 실제 물리 메모리에서 할당 가능한 영역에 할당해주는 방식
  * 가상 메모리는 64bit architecture에서는 $$0 \sim 2^{64} -1$$ 의 주소값을 가질 수 있음
* $$\textrm{물리 메모리 크기} \leq \textrm{가상 메모리 크기}$$
  * 그렇다면 **과할당 상태**는 어떻게 극복?
  * secondary storage에 swap in, out하는 기법을 사용
  * swapping을 위해서 정형화된 형식으로 secondary storage에 저장할 필요성이 생김
    * **<u>Paging</u>** 기법의 등장



## MMU?

* $$\textrm{가상 메모리 주소} \rightarrow \textrm{물리 메모리 주소}$$ 변환의 필요성 대두
* 특정 프로세스가 다른 프로세스의 메모리 영역에 접근할 수 없도록 할 필요성
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-08-12 at 12.53.57 AM.png" alt="Screenshot 2024-08-12 at 12.53.57 AM" style="zoom:67%;" />
  * hardware적인 도움을 받아서 이를 구현
* user mode에서 발생하는 memory address는 모두 logical address
  * MMU에서 이를 실제 메모리에 할당시켜야하는데, 가장 간단하게는 아래와 같이 `relocation register` 를 활용하여 런타임 매핑
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-08-12 at 1.01.31 AM.png" alt="Screenshot 2024-08-12 at 1.01.31 AM" style="zoom:50%;" />
* MMU는 정책에 따라서, 최대한 효율적으로 연속적인 메모리를 할당
  * 프로세스가 가지는 실제 주소 공간의 크기만큼 연속적인 메모리에 할당됨
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-08-12 at 4.08.59 AM.png" alt="Screenshot 2024-08-12 at 4.08.59 AM" style="zoom:50%;" />
  * 이 때, 무작정 연속된 영역에 메모리를 할당하는 것은 `external fragmentation` 을 만들어낸다.
    * 위 그림의 마지막 단계에서, 실제 할당 가능한 메모리 영역은 충분하지만, 연속된 메모리 영역이 아니여서 메모리 할당이 불가능한 영역을 뜻함
  * 이를 최소화하기 위한 3가지 방법이 존재
    * `first fit`, `best fit`, `worst fit`
    * 3가지 방법 중에 무엇이 더 우월하다는 것은 없음
  * 그럼에도 불구하고 External Fragmentation을 완전하게 해결할 수 있는 방법은 없다.



## Paging?

* <u>External Fragmentation을 완전하게 피해갈 수 있는 방법</u>
* 핵심은 프로세스의 주소공간을 연속된 메모리 영역에 할당하지 않는 것
* logical address space를 **고정 크기의 Page로 분할**
* physical address space를 **고정 크기의 Frame으로 분할**
* [일반적으로 1개의 page 크기와 Frame 크기는 동일하다](https://cs.stackexchange.com/questions/47541/is-page-size-always-equal-to-frame-size)
* hardware + Operating system 가 결합되어 페이징 기법을 구현함



<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-08-12 at 4.23.44 AM.png" alt="Screenshot 2024-08-12 at 4.23.44 AM" style="zoom:67%;" />

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot 2024-08-12 at 4.23.37 AM.png" alt="Screenshot 2024-08-12 at 4.23.37 AM" style="zoom:67%;" />

* CPU가 만들어내는 모든 address는 `{page number, page offset}` 의 정보를 가진다.
* paging hardware에서 `page number` 값을 참조하여 page table에서 실제 프레임과 매핑시킨다음, 모든 과정에서 불변 값인 `page offset` 값을 통해서 실제 물리 메모리 영역을 참조한다.

* 고정 크기의 page와 frame이라는 단위를 사용하기 때문에, 할당 받은 메모리의 크기가 실제 메모리 영역보다 큰 `internal fragmentation`이 발생한다.
  * 이것을 방지하기 위해서, page table의 크기를 적절하게 조절한다.
  * e.g.) linux: $$2^{12} = 4096\textrm{바이트}$$ 



## 면접 예상 질문

<details>
	<summary>가상 메모리에 대해서 설명해주세요</summary>
	<ul>
		<li>가상 메모리는 메모리 관리를 위한 논리적인 메모리 영역입니다. 프로세스는 실제 물리 메모리의 특정 위치에 데이터를 적재하는 것이 아닌 가상 메모리의 주소를 통해서 데이터를 적재하고 MMU가 이를 실제 물리 메모리에 매핑해주는 방식입니다.
		</li>
	</ul>
</details>

<details>
	<summary>MMU에 대해서 설명해주세요</summary>
	<ul>
		<li>
			MMU는 논리 메모리 주소를 물리 메모리 주소로 매핑해주는 역할을 수행합니다.
			이를 통해서, 유저 프로세스는 실제 물리 메모리 주소를 고려하지 않고도 데이터를 적재할 수 있게 됩니다.
        </li>
	</ul>
</details>

<details>
		<summary>TLB는 무엇인가요?</summary>
	<ul>
		<li>
			TLB는 MMU가 가상 메모리 주소를 물리 메모리 주소로 매핑해줄 때, 참조하는 일종의 캐시입니다. 현대의 OS는 hierarchical한 페이지 테이블 구조를 사용하는데, 이 과정에서 N-level Page table구조일 경우 N번의 페이지 테이블 참조가 발생합니다. 성능 최적화를 위해서 한 번 매핑된 정보를 캐싱하여 추후 접근 시, 캐싱된 정보를 이용하기 위해서 TLB가 이용됩니다.
        </li>
	</ul>
</details>