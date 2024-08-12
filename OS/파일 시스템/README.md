#  파일 시스템



## Prerequisite

* 데이터는 파일 형태로 저장 장치에 저장
* 파일 수 증가 ➡️ 디렉토리 개념 필요 ➡️ **파일시스템** 필요
  * 파일을 편리하고 효과적으로 생성/수정/접근/삭제 할 수 있도록 관리하는 체계



## 주요 특징

* 저장 공간 관리 및 접근
  * 저장 장치의 효율적인 활용, 특정 파일/디렉토리 접근 등
* 메타데이터 활용
  * 파일과 관련된 부가 정보 관리
* 다양한 부가 기능 제공
  * 압축, 암호화, 저널, 동적 할당, 다국어 지원 등

* 파일 시스템 또한 파일을 포함하도록 할당된 하드디스크 영역의 일부
  * 일반 파일과 동일하게 마운트됨
  * 현대의 파일 시스템은 **트리 구조의 디렉토리**를 채택
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/basea14.jpg" alt="basea14" style="zoom:50%;" />
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%207.20.04%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 7.20.04 PM" style="zoom:50%;" />



## 파일 시스템 내부 구조

* `메타` 영역 + `데이터` 영역

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%207.33.10%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 7.33.10 PM" style="zoom:80%;" />

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%207.44.18%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 7.44.18 PM" style="zoom:80%;" />

* 메타영역
  * 파일의 메타데이터가 저장되는 영역
  * e.g.) 파일의 이름, 위치, 크기, 속성, 시간 정보, 삭제 유무 등
* 데이터 영역
  * 파일의 실제 데이터가 저장되는 영역
* **파일을 열거나 실행하기 전까지는 메타 정보만을 이용하여 탐색**
* 파일을 실행하면 메타 정보를 기반으로 실제 데이터 접근



## 데이터 처리 단위

> `클러스터`, `블록`, `페이지`
{: .prompt-info}




* **일반적으로 <u>저장 장치</u>는 `섹터`라는 최소한의 데이터 입/출력 단위를 사용**
  * 일반적으로 512Byte (4 KiB 등도 존재)
* **파일 시스템은** 입/출력을 위해 클러스터, 블록, 페이지 등의 단위를 사용

  * e.g.) 4mb 를 쓰기위해서 $$\textrm{512byte} \rightarrow \textrm{8,192번}$$ /  $$\textrm{4KiB} \rightarrow \textrm{1,024번}$$
* <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%208.39.52%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 8.39.52 PM" style="zoom:80%;" />



* 클러스터는 파일과 폴더에 대해서 디스크 공간을 할당하는 단위
  * 논리적으로 연속된 섹터들을 하나로 묶어서 사용하는 방식
* **슬랙**
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%208.52.59%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 8.52.59 PM" style="zoom:50%;" />
  * 고정 길이 처리 단위 때문에 발생하는 낭비되는 공간



## 파일 시스템 종류

* MS-DOS

  * FAT(File Allocation Table)
  * 파일의 위치 정보를 기록한 테이블을 이용한 단순한 구조
  * 구조가 단순하여 액세스 속도가 빠르다
  * 하지만, 안전성과 보안성이 다른 파일 시스템에 비해서 취약하고 용량이 작다.

  

* Windows

  * NTFS (New Technology File System)
  * FAT의 한계점을 개선한 파일 시스템
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-08-12%20at%209.00.41%E2%80%AFPM.png" alt="Screenshot 2024-08-12 at 9.00.41 PM" style="zoom:50%;" />

> 파일 시스템마다, 파일을 실제 관리하는 방식의 세부사항이 다르므로 필요에 따라서 추가적으로 학습을 진행하는 것이 적절하겠다.
{: .prompt-warning}


## 면접 예상 질문

<details>
	<summary>
		file.open() 시스템 콜이 호출되었을 때의 동작 과정을 간략하게 설명해주세요
	</summary>
	<ul>
        <li>
            OS는 열린 파일들을 관리하기 위한 open file table을 메인 메모리에 적재합니다. file을 열기 전, 경로에 파일이 존재하는지 확인한 다음, 해당 파일의 메타데이터를 Open file table에 저장합니다. 추후 read/write 연산이 이루어질 때 마다, open file table을 참조하여 실제 파일의 내용을 참조합니다.
        </li>
    </ul>
	</summary>
</details>

<details>
	<summary>
		파일 시스템은 왜 필요할까요?
	</summary>
	<ul>
		<li>
			컴퓨터에서 데이터를 효과적이게 저장하고 관리하기 위해서 필요합니다. 파일 시스템에서는 메타데이터와 실제 데이터를 함께 관리합니다. 이를 통해서 사용자는 파일과 관련된 부가 정보들을 함께 활용할 수 있고, 실제 데이터를 압축해서 저장하는 등 다양한 부가 기능들을 제공하고 있습니다. 
		</li>
		<li>
			그렇기에 현대의 컴퓨터에서는 많은 수의 파일을 쉽게 관리하기 위한 수단으로 파일 시스템을 운영체제마다 개별적으로 선정하여 운영하고 있습니다.
		</li>
	</ul>
</details>