---
title: "[CS] Process vs. Thread"
description: ""
author: aijinsol
date: 2023-03-21
categories:
  - ComputerScience
tags:
  - program
  - process
  - thread
  - multiprocess
  - multithread
  - pcb
  - context-switching
  - ipc
---

> CS 기술면접 단골질문인 `Program`, `Process`, `Thread`의 차이와 기본적인 작동방식에 대해 설명할 줄 알아야 한다.
> + "운영체제가 시스템 자원을 어떤 방식으로 할당하고, 실제로 프로그램은 이 자원을 어떤 방식으로 활용하여 작동할까?"
> + `Program`: 아직 실행되지 않은 프로그램
> + `Process`: 실행 중인 프로그램. 운영체제로부터 자원을 할당받은 `작업`의 단위
> + `Thread`: 프로세스가 할당받은 자원을 이용하는 `실행 흐름`의 단위

<br>

# 1. `Program`

`Program`: 파일이 저장장치에 저장되어 있지만, `메모리에는 올라가 있지 않은` `정적인 상태`. 즉, 아직 실행되지 않은 파일 그 자체로 그냥 코드 덩어리.

- `메모리에 올라가 있지 않은`: 아직 운영체제가 프로그램에게 독립적인 메모리 공간을 할당해주지 않은
- `정적인 상태`: 움직이지 않는 상태. 실행되지 않고 가만히 있는 상태

<br>

# 2. `Process`

프로그램에 의미를 부여하기 위해 프로그램을 실행해보자. (= `프로세스를 생성한다`)

`Program`이 실행되기 전까지는 보조기억장치에 있는 데이터 덩어리일 뿐! 하지만, `program`을 실행하는 순간 해당 파일이 컴퓨터 메모리에 올라가게 되고, 이러한 `동적인 상태`의 프로그램을 `process`라고 한다. 즉, `process`는 `실행 중인 프로그램`.

![fig1](/statics/posts/cs/cs-01-fig1.png){: width="400"}

<br>

## 1) PCB (프로세스 제어 블록)

모든 `process`는 실행을 위해 CPU를 필요로 하지만, CPU 자원은 한정되어 있다. 따라서 `process`들은 차례대로 돌아가며 한정된 시간만큼만 CPU를 이용한다. 자신의 차례가 되면 정해진 시간만큼 CPU를 이용하고, 시간이 끝났음을 알리는 `Timer interrupt`가 발생하면 다음 `process`에 차례를 넘기고 기다린다.

운영체제는 빠르게 번갈아 수행되는 `process`의 실행순서를 관리하고, `process`에 CPU를 비롯한 자원을 배분한다. 이를 위해 운영체제는 `PCB(Process Control Block)`을 이용한다.

- `PCB`: `Process`와 관련된 정보를 저장하는 자료구조
    - 커널 영역에 생성된다
    - 운영체제가 수많은 `process` 들 사이에서 `PCB`로 특정 `process`를 식별하고 해당 `process`를 처리하는 데 필요한 정보를 판단한다
    - `Process` 생성 시에 만들어지고 실행 끝남과 동시에 폐기된다.
        - 즉, ‘새로운 프로세스가 생성되었다’ = ‘운영체제가 PCB를 생성했다’
        - ‘프로세스가 종료되었다’ = ‘운영체제가 PCB를 폐기했다’

- `PCB`에 담기는 정보
    
    1) `PID(Process ID)`: `Process`를 식별하기 위한 고유번호
    
    2) `레지스터 값`: `Process`는 자신의 실행 차례가 돌아오면 이전까지 사용했던 레지스터의 중간값들을 모두 복원한다. 그래야 이전까지 진행했던 작업을 이어서 할 수 있다
    
    3) `Process 상태`: 현재 `process`가 입출력 장치를 사용하기 위한 대기 상태, CPU를 사용하기 위한 대기 상태, CPU 이용 중 상태인지 등
    
    4) `CPU 스케쥴링 정보`: `Process`가 언제, 어떤 순서로 CPU를 할당받는지에 대한 정보
    
    5) `메모리 관리 정보`: `Process`마다 메모리에 저장된 위치가 다르므로 `process`가 어느 주소에 저장되어 있는지에 대한 정보
    
    6) `사용한 파일과 입출력장치 목록`: `Process`가 실행과정에서 특정 입출력장치나 파일을 사용한 정보
    
![fig2](/statics/posts/cs/cs-01-fig2.png){: width="600"}

<br>

## 2) `Context Switching`

- `Context Switching`: 기존 `process`의 `context`를 `PCB`에 백업하고, 새로운 `process`를 실행하기 위해 context를 `PCB`로부터 복구하여 새로운 `process`를 실행하는 것
    - 하나의 `process`에서 다른 `process`로 실행 순서가 넘어가면 먼저 실행되던 process A는 프로그램 카운터를 비롯한 각족 레지스터 값, 메모리 정보, 실행을 위해 열었던 파일이나 사용한 입출력 장치 등 지금까지의 중간 정보를 백업한다. 그래야 다음 차례가 왔을 때 이전까지 실행했던 내용에 이어 다시 실행 재개 가능. 이러한 중간 정보를 `context`라고 한다.
    - `Context Switching`은 여러 `process`가 끊임없이 빠르게 번갈아 가며 실행되는 것으로, `process`가 그만큼 빨리 번갈아 수행되기 때문에 여러 `process`가 동시에 실행되는 것처럼 보인다

![fig3](/statics/posts/cs/cs-01-fig3.png){: width="350"}

<br>

## 3) Process의 메모리 영역

`Process`가 생성되면 커널 영역에 `PCB`가 생성되고, 사용자 영역에는 `process`가 `Code/Data/Heap/Stack` 영역으로 나뉘어 저장된다.

### Code 영역

- 실행할 수 있는 코드가 저장된다
- 데이터가 아닌 CPU가 실행할 명령어가 있기 때문에 쓰기가 금지되어 있다. 즉, read-only (읽기전용) 공간이다
- `Process` 실행 과정에서 크기가 변하지 않는 정적 할당 영역

### Data 영역

- `Program`이 실행되는 동안 유지할 데이터가 저장되는 공간 ex) 전역변수
- `Process` 실행 과정에서 크기가 변하지 않는 정적 할당 영역

### Heap 영역

- 프로그래머가 직접 할당할 수 있는 저장 공간
- 프로그래밍 과정에서 메모리 공간을 할당했다면 언젠가는 해당 공간을 반환해야 한다.
    - 메모리 공간을 반환한다 = ‘더 이상 해당 메모리 공간을 사용하지 않겠다’라고 운영체제에 말해주는 것
    - 메모리 공간을 반환하지 않는다면 할당한 공간은 메모리에 계속 남아 메모리 낭비를 초래한다 (Memory Leak)
- `Process` 실행 과정에서 크기가 변할 수 있는 동적 할당 영역

### Stack 영역

- 데이터를 일시적으로 저장하는 공간 ex) 지역변수
- 일시적으로 저장할 데이터는 stack 영역에 PUSH되고, 더 이상 필요하지 않은 데이터는 POP되면서 stack 영역에서 사라진다
- `Process` 실행 과정에서 크기가 변할 수 있는 동적 할당 영역

![fig12](/statics/posts/cs/cs-01-fig12.png){: width="350"}

<br>

## 4) Process 상태

`Program`이 실행될 때 여러 Process들이 빠르게 번갈아 가면서 실행된다. 그 과정에서 하나의 Process는 여러 상태를 거치면서 실행된다.

### New (생성 상태)

- `Process`를 생성 중인 상태. 이제 막 메모리에 적재되어 `PCB`를 할당받은 상태
- 생성 상태를 거쳐 실행할 준비가 완료된 `process`는 곧바로 실행되지 않고 `Ready` 상태로 넘어가 CPU의 할당을 기다린다

### Ready (준비 상태)

- 당장이라도 CPU를 할당받아 실행할 수 있지만, 아직 차례가 아니라 기다리고 있는 상태
- 차례가 되면 CPU를 할당받아 `Running` 상태로 넘어간다

### Runnning (실행 상태)

- CPU를 할당받아 실행 중인 상태
- 할당된 일정 시간동안 CPU를 사용할 수 있다
- `Process`가 할당된 시간을 모두 사용한다면(`timer interrupt`가 발생하면) 다시 `Ready` 상태가 되고, 실행 도중 입출력 장치를 사용하여 입출력 장치의 작업이 끝날 때까지 기다려야한다면 `Blocked` 상태가 된다.

### Blocked (대기 상태)

- 특정 이벤트가 일어나길 기다리는 상태 (입출력 장치의 작업을 기다리는 상태)
- `Process`는 실행 도중 입출력 장치를 사용하는 경우가 있는데 입출력 작업은 CPU에 비해 처리 속도가 느려 입출력 작업을 요청한 `process`는 입출력장치가 입출력을 끝낼 때까지 (입출력 완료 interrupt를 받을 때까지) 기다려야 한다
- 입출력 작업이 완료되면 해당 `process`는 다시 `Ready` 상태로 CPU 할당을 기다린다

### Terminated (종료 상태)

- `Process`가 종료된 상태
- `Process`가 종료되면 운영체제는 `PCB`와 `process`가 사용한 메모리를 정리한다

![fig4](/statics/posts/cs/cs-01-fig4.png){: width="450"}

<br>

## 5) Process 계층 구조

- `Process`는 실행 도중 시스템 호출을 통해 다른 `process`를 생성할 수 있다
    - `Parent Process` (부모 프로세스): 새로운 process를 생성한 `process`
    - `Child Process` (자식 프로세스): 부모 프로세스에 의해 생성된 `process`
- `부모와 자식 process`는 서로 다른 `process`이기에 서로 다른 `PID`를 가진다
- `Parent process`로부터 생성된 `child process`는 실행 과정에서 또 다른 `child process`를 생성할 수 있고, 그 `child process`는 실행 과정에서 또 다른 `child process`를 생성할 수 있다.
    
    → 이처럼 많은 운영체제가 `process`가 `process`를 낳는 계층적인 구조로 `process`들을 관리한다
    
- 컴퓨터가 부팅될 때 실행되는 최초의 `process`가 `child process`를 생성하면서 시작

![fig5](/statics/posts/cs/cs-01-fig5.png){: width="500"}

<br>

## 6) Process 생성 기법

- `Parent process`를 통해 생성된 `child process`들은 `fork`와 `exec`를 통해 실행된다.
    - `fork`: 자기 자신 process의 복사본을 만드는 시스템 호출
        - `Parent process`는 `fork`를 통해 자신의 복사본을 `child process`로 생성해 낸다
    - `exec`: 자신의 메모리 공간을 새로운 프로그램으로 덮어쓰는 시스템 호출
        - 만들어진 복사본(`child process`)은 `exec`를 통해 자신의 메모리 공간을 다른 program으로 교체한다
        - `exec`가 호출되면 `code 영역`과 `data 영역`의 내용이 실행할 `program`의 내용으로 바뀌고, 나머지 영역은 초기화 된다
- `Child process`는 `parent process`의 자원들을 상속하지만, PID 값이나 저장된 메모리 위치는 다르다.

![fig6](/statics/posts/cs/cs-01-fig6.png){: width="550"}

<br>

# 3. `Thread`

과거에는 `program`을 실행할 때 실행 시작부터 끝까지 하나의 프로세스만 사용해서 일이 처리되었다. 하지만 하나의 `process`만 사용해서 프로그램을 실행하기에는 단순히 한 작업 밖에 못했었다. 그래서 `process`보다 더 작은 단위인 `thread`라는 개념이 도입되었다. `Thread` 덕분에 하나의 `process`가 한 번에 여러 일을 동시에 처리할 수 있게 되었다.

+ `Thread`는 `process` 내에서 서로 다른 스레드 ID, 프로그램 카운터 값을 포함한 레지스터 값, 스택을 갖고 있어서 `thread`마다 서로 다른 코드를 실행할 수 있다.
+ `Thread`는 `process` 의 자원을 공유한다. 즉, 한 `process` 내의 `thread`는 `Code/Data/Heap` 영역을 공유한 채 실행에 필요한 최소한의 정보(스레드 ID, 프로그램 카운터 값을 포함한 레지스터 값, 스택)를 이용해 실행된다.

<div style="display: flex; gap: 10px;">
  <img src="/statics/posts/cs/cs-01-fig7.png" alt="fig7" width="350">
  <img src="/statics/posts/cs/cs-01-fig8.png" alt="fig8" width="350">
</div>

<br>

# 4. `Multiprocess` vs. `Multithread`

> + `Multiprocess`: 여러 `process`를 동시에 실행하는 것
> + `Multithread`: 여러 `thread`로 `process`를 동시에 실행하는 것


동일한 작업을 수행하는 단일 `thread`의 `process` 여러 개를 실행하는 것과 하나의 `process`를 여러 `thread`로 실행하는 것은 무엇이 다를까?

- `Process`를 fork하여 같은 작업을 하는 동일한 `process` 여러 개를 동시에 실행하면 `Code/Data/Heap` 영역을 비롯한 모든 자원이 복제되어 메모리에 적재된다. 같은 프로그램을 실행하기 위해 메모리에 동일한 내용들이 중복해서 여러 개씩 존재하는 것은 낭비!
- 하지만, `thread`는 스레드 ID, 프로그램 카운터 값을 포함한 레지스터 값, 스택의 정보만 서로 다를 뿐 `process`가 가지고 있는 자원을 공유한다.

<div style="display: flex; gap: 20px; align-items: center; text-align: center;">
  <div>
    <img src="/statics/posts/cs/cs-01-fig9.png" alt="Multiprocess" width="500">
    <p>Multiprocess</p>
  </div>
  <div>
    <img src="/statics/posts/cs/cs-01-fig10.png" alt="Multithread" width="500">
    <p>Multithread</p>
  </div>
</div>

<br>

## 1) `Multithread` 장단점

- 장점
    - 여러 `process`를 병행 실행하는 것보다 메모리를 효율적으로 사용 가능
    - 서로 다른 `process` 들은 기본적으로 자원을 공유하지 않기 때문에 서로 독립적으로 실행되지만, `thread`는 `process`의 자원을 공유하기 때문에 서로 협력과 통신에 유리하며, 응답시간이 빠르다
- 단점
    - `Multiprocess` 환경에서는 하나의 `process`에 문제가 생겨도 다른 `process`에는 지장이 적거나 없다. 하지만, `multithread` 환경에서는 하나의 `thread`에 문제가 생기면 `process` 전체에 문제 발생 가능성! 모든 `thread`는 `process`의 자원을 공유하고, 하나의 `thread`에 문제가 생기면 다른 `thread`도 영향을 받기 때문.
    - 자원을 공유하기 때문에 `Synchronization Issue(동기화 문제)` 발생 가능성
        - `Synchronization Issue(동기화 문제)`: 여러 `thread`가 함께 전연변수를 사용할 경우 발생할 수 있는 충돌. 예를 들어, multithread를 사용하면 각각의 thread 중 어떤 것이 어떤 순서로 실행될지 그 순서를 알 수 없다. 만약 thread A가 어떤 자원을 사용하다가 thread B로 제어권이 넘어간 후 thread B가 해당 자원을 수정했다면 다시 제어권을 받은 thread A가 해당 자원에 접근하지 못하거나 바뀐 자원에 접근하게 되는 오류가 발생할 수 있다.
        - 스케쥴링은 운영체제가 자동으로 해주지 않기 때문에 프로그래머가 직접 적절한 기법을 직접 구현하여 multithread 사용시 동기화 문제에 대응할 수 있도록 해야한다.

![fig11](/statics/posts/cs/cs-01-fig11.png){: width="500"}

<br>

## 2) `IPC` (프로세스 간 통신)

기본적으로 `process`끼리는 자원을 공유하지 않지만, `process` 끼리도 자원을 공유하고 데이터를 주고 받을 수 있다! 이것을 `IPC(Inter-Process Communication)`이라고 한다.

어떤 방식으로 통신이 이루어질까?

- `파일을 통한 통신`: 예를 들어 Process A에는 ‘test.txt’ 파일에 값을 쓰는 프로세스. Process B는 ‘test.txt’ 파일을 읽는 프로세스라면 두 프로세스는 ‘test.txt’ 파일 속 데이터를 주고 받으므로 프로세스 간의 통신이 이루어진다.
- `공유 메모리`: 예를 들어 Process A와 Process B가 공유하는 메모리 영역 내에 ‘name’이라는 전역변수가 있다하자. Process A가 ‘name’ 안에 값을 저장한 뒤, Process B가 ‘name’ 변수 값을 읽어들인다면 두 Process는 전역변수 ‘name’을 통해 값을 주고받으며 통신한 것.

즉, `process` 간의 통신은 모든 자원을 애초에 공유하고 있는 `thread`에 비해 까다로운 것일 뿐 불가능한 것은 아니다!

<br>

# Reference

+ [혼자 공부하는 컴퓨터 구조+운영체제](https://product.kyobobook.co.kr/detail/S000061584886)
+ [개발장 - 프로세스와 스레드의 차이](https://velog.io/@raejoonee/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%B0%A8%EC%9D%B4)
