## 1. 컴퓨터 시스템 구조

![img](https://blog.kakaocdn.net/dn/bbDCWZ/btqIpv4igBz/VzxEiwKfaVi7utgy29Nf9K/img.png)            

​    

### 1.1. Computer (전문가적 입장에서)

* CPU : 매 클럭 사이클마다 Memory에서 Instruction(기계어)을 읽어서 실행한다. Memory 및 I/O device의 local buffer에 접근할 수 있다.

* Memory : CPU의 작업 공간이다. 원래는 CPU만 접근 가능한 공간이지만 DMA controller가 있다면 이 역시 접근이 허용된다.

### 1.2. I/O device 

* 키보드, 마우스 : 입력장치

* 모니터, 프린터 : 출력장치

* 디스크 : 보조기억장치이자 입출력장치 (디스크에서 내용을 읽으면 입력장치, 디스크에 내용을 저장하면 출력장치)

​    

## 2. 컴퓨터 시스템 구조 (더 자세하게)

![img](https://blog.kakaocdn.net/dn/nHrbt/btqIdsnzuXp/OZRN93Nw2hhFG2dUxz6iyK/img.png)

​     

### 2.1. I/O device

* device controller (장치 제어기) → **Hardware!**

  > 해당 I/O device를 전담하여 관리하는 일종의 작은 CPU이다. 자신의 **local buffer만 접근 가능**하다.

  * control register, status resgister를 가짐 (CPU가 지시하는 제어 정보 및 명령을 관리하고 수행하는 register)
  * local buffer를 가짐 (실제 data를 저장하고 담는 register)

* local buffer : device controller의 작업 공간이다.    

> device driver (장치 구동기) 
>
> 다양한 제조사의 디바이스는 각각의 디바이스를 처리하기 위한 개별 인터페이스를 갖고 있는데, OS에 설치하는 프로그램 중 각각의 디바이스에 올바른 인터페이스로 접근할 수 있게 해주는 소프트웨어를 지칭한다. → **Software!**     

### 2.2. Computer

* CPU의 흐름

  내가 만든 C 프로그램이 컴파일 되어 A라는 프로그램으로서 CPU를 차지하고 실행되고 있다면 CPU는 메모리상에서 A 프로그램의 Instruction들을 읽으며 작업을 수행한다. CPU는 메모리에 접근하는 Instruction들만 수행하며, 디스크 읽기 및 쓰기(File I/O), 키보드 입력(scanf), 모니터 출력(printf) 등의 I/O device와 관련된 Instruction들은 device controller로 보낸다. Device controller는 local buffer에서 CPU가 요청한 작업을 수행하고, 그동안 CPU는 다시 메모리에 접근해 I/O 관련 결과가 필요 없는 다음 Intruction들을 수행한다. 만일 A 프로그램에서 반드시 device controller에 보낸 I/O Instruction의 결과가 나와야 작업을 수행할 수 있는 상황이 되면, 먼저 작업을 수행할 수 있는 B 프로그램으로 CPU 자원이 옮겨가게 된다.(= time sharing) 결과적으로, CPU는 프로그램이 종료될 때까지 메모리상의 프로그램들 중 자신이 할 수 있는 Instruction을 끊임없이 찾아 수행하며 여러 프로그램들을 동작하게 한다.

* register : CPU 안에 있는 Memory보다 더 빠르면서 정보를 저장할 수 있는 작은 공간 

* mode bit

  사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호장치이다. CPU에서 현재 실행되고 있는 프로그램이 운영체제인지 사용자 프로그램인지 구분해주는 역할을 수행하며 두 가지 모드를 지원한다.

  * 사용자 모드 [1] : 사용자 프로그램 수행 (Only 일반명령만 수행한다.)
    * 메모리에 접근하는 Instruction들만 허용한다.
    * 사용자 프로그램에게 CPU를 넘기기 전에 mode bit을 1로 세팅한다.
  * 모니터 모드 [0] (= **커널 모드**, 시스템 모드) : OS 코드 수행 (특권명령까지 포함해 모든 명령이 수행 가능하다.)
    * I/O 관련 Instruction 같이 보안을 해칠 수 있는 중요한 명령어를 포함해 모든 Instruction이 수행 가능하다.
    * Interrupt나 Exception 발생 시 하드웨어가 mode bit을 0으로 바꾼다.

* Intrerrupt line : 인터럽트 요청을 담는 공간. CPU는 Interrupt line을 확인하고 요청된 인터럽트를 처리한다.

* timer : 특정 프로그램이 CPU를 독점하는 것(ex. 무한루프)을 막기 위해 시간을 바탕으로 제어하는 장치

  * 타이머 값은 매 클럭 틱마다 1씩 감소하고 값이 0이 되면 인터럽트가 발생한다.

  * time sharing 구현에 널리 이용된다.

  * 현재 시간 계산을 위해서도 사용된다.

  * 흐름

    처음 컴퓨터 부팅 시에는 운영체제가 CPU를 가지고 있다. 그 후 특정 사용자 프로그램에 CPU를 넘길 때, timer에 최대 사용 시간(ex. 수십 밀리세컨드의 짧은 시간)을 세팅하고 CPU를 넘긴다. timer는 설정된 시간이 되면 interrupt line을 통해 CPU에 interrupt를 건다. CPU는 할당받은 프로그램의 Instruction를 하나 수행하고 Interrupt line을 체크하는 과정을 반복하며, Interrupt line에서 timer의 interrupt를 발견 시 하던 일을 멈추고 운영체제에게 CPU 제어권을 넘긴다. 그리고 운영체제는 다시 timer에 특정 값을 세팅하고 다른 프로그램에게 CPU를 넘기는 흐름을 반복한다.

    운영체제는 다른 프로그램에게 자유롭게 CPU를 넘기지만, 마음대로 다시 뺏어 올 수는 없기 때문에 timer의 도움을 받아 다시 권한을 가져온다. 이외에도 프로그램이 종료될 때나 I/O 관련 Instruction을 만났을 때는 timer에 상관없이 CPU가 자동으로 운영체제에 반납된다. 사용자 프로그램은 각종 보안 이슈 등의 이유로 직접 I/O 장치에 대한 접근을 할 수 없기 때문에, 관련 Instruction을 만나면 운영체제에게 CPU를 넘기고 운영체제는 I/O device controller에 작업을 요청한다. 그 후 I/O device controller에 의해 사용자가 버퍼에 결과물(ex. 키보드 입력 데이터)을 남기고 controller가 다시 CPU에 intrrupt를 걸 때까지, 운영체제는 다른 사용자 프로그램에 CPU를 넘긴다. CPU는 다른 프로그램에서 Instruction들을 수행하다가 Interrupt line에서 device controller의 interrupt를 확인하면 우선은 CPU를 운영체제에 넘긴다. 운영체제는 I/O 요청 작업이 완료됨을 인지하고 그 결과물을 I/O 작업을 요청했던 프로그램의 메모리 공간에 복사해둔다. 그리고 당장은 timer에 남은 시간만큼 방금 수행 중이던 프로그램에게 도로 CPU를 넘긴다. 하지만, 그 후에는 CPU를 다시 반납받아 I/O 작업을 요청했던 프로그램에게 CPU를 돌려준다. 그리고 해당 프로그램은 메모리 공간에 복사되어 있는 I/O 작업 결과물을 사용하여 다음 Instruction들을 수행한다.

* DMA controller (Direct Memory Access)

  CPU에 너무 많은 인터럽트가 걸리는 것을 방지하기 위해, 로컬 버퍼의 데이터를 메모리에 복사하는 작업을 대신 수행하고 완료된 작업들을 일정량 모아뒀다가 한 번에 CPU에 인터럽트를 걸어 CPU의 동작이 효율적으로 운영되도록 도와주는 기능을 한다.

* memory controller : CPU와 DMA controller의 동시 접근을 막고 교통정리를 해주는 일종의 조율기 역할을 한다.

​    

## 3. 입출력(I/O)의 수행

* 모든 입출력 명령은 특권 명령이다.

* 시스템 콜(system call) : 사용자 프로그램이 운영체제에게 보내는 I/O 요청

* trap을 사용하여 인터럽트 벡터의 특정 위치로 이동

* 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동

* 올바른 I/O 요청인지 확인 후 수행

* 작업 완료 후 제어권을 시스템 콜 다음 명령으로 옮김

​    

## 4. 인터럽트 (주로 하드웨어 인터럽트)

인터럽트 당한 시점의 레지스터와 program counter를 save한 후, CPU 제어권을 인터럽트 처리 루틴(해당 인터럽트를 처리하는 커널 함수)에 넘긴다.

* Interrupt (하드웨어 인터럽트): 하드웨어가 발생시킨 인터럽트 ex) I/O device controller의 인터럽트, timer의 인터럽트 

* Trap(소프트웨어 인터럽트)
  * Exception : 프로그램이 오류를 범한 경우
  * System call : 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것 

* 인터럽트 관련 용어

  * 인터럽트 벡터 : 해당 인터럽트 처리 루틴의 주소(필요한 함수 주소)를 가지고 있다.

  * 인터럽트 처리 루틴 (= interrupt service routine, 인터럽트 핸들러)

    * 해당 인터럽트를 처리하는 커널 함수 (운영체제 내 코드)

    > 키보드 컨트롤러 인터럽트라면 키보드 버퍼 내용을 메모리로 카피하고 키보드 I/O를 요청했던 프로세스에게는 CPU를 얻을 수 있음을 표시한다. 타이머의 인터럽트라면 CPU를 뺐어서 다른 프로그램에게 전달한다.

​    

> 현대의 운영체제는 인터럽트에 의해 구동된다! (인터럽트가 없을 때는 항상 사용자 프로그램이 CPU를 점유하므로...)

​    

## Reference

[운영체제, 이화여대 반효경 교수님](http://www.kocw.net/home/search/kemView.do?kemId=1046323)