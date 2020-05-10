---
title: "운영체제-15 Deadlock"
excerpt: "OS L12 S1 - L12 S2"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-01T20:15:00+09:00
toc: true
---

## 개요

- **Deadlock**

  - **정의**: 프로세스들이 더 이상 진행하지 못하고 영구적으로 블록되어 있는 상태
  - 특정 집합 내의 프로세스들이 서로간의 시스템 자원에 대한 경쟁이나 서로간의 진행을 기다리고 있는 경우 발생할 수 있다.
  - 여러 프로세서들이 concurrent 하게 수행될 때 발생. 멀티 프로세서 환경 뿐만 아니라 단일 프로세서에서 프로세스들의 인터리빙중에도 발생할 수 있다. 
  - 자원을 공유하는 경우 발생할 가능성이 높음

- **Deadlock example with semaphore**

  - |   Process 0   |   Process 1   |
    | :-----------: | :-----------: |
    | P(Resource 0) | P(Resource 1) |
    | P(Resource 1) | P(Resource 0) |

  - **Resource Allocation Graph**

    - 사각 박스: 프로세스
    - 둥근 박스: 자원
    - 자원→프로세스: 자원이 프로세스에 할당됨
    - 프로세스→자원: 프로세스가 자원 사용을 요구
    - ![images](/assets/images/resource-allocation-graph.png)
    - 그래프에서 cycle이 존재하는 경우 deadlock이 발생한 상황임을 알 수 있음. 단, 자원을 관리하는 semaphore가 binary semaphore인 경우에만 반드시 deadlock이 발생.
    - 위의 경우는 아주 단순한 경우이다. 세마포어가 counting semaphore이거나 자원이 메모리처럼 uncountable resource인 경우에도 deadlock이 발생할 수 있기때문에 운영체제가 처리하기 힘들다.

- **etc**

  - **Deadlock**: 프로세스들이 공유 자원을 서로 분점하고 있기 때문에 프로세스의 진행이 멈출 뿐 추가적인 리소스의 낭비는 없다(ex: CPU resource)
  - **Livelock**: CPU 자원을 계속 소모하며 아무 일도 하지 않는 상태
    - Receive livelock: 큐에서 처리되는 패킷보다 들어오는 패킷이 많아지면서 인터럽트 핸들러만 동작하여 패킷의 처리(유저 프로그램)를 수행할 수 없는 경우
    - Interrupt coalescing: 인터럽트 발생 빈도를 줄이기 위해서, 여러 번의 입력을 모아서 한번에 인터럽트를 발생시키거나 일정 시간마다 한번씩 인터럽트를 발생시키는 기법
  - **Indefinite** **postponement**: 프로세스가 절대 일어날 수 없는 이벤트를 기다리는 상황(ex: 응답을 기다리는 프로세스가 갑작스럽게 종료되는 경우 등)



## Deadlock Handling

**deadlock의 필요조건**

- Mutual exclusion
  - 한 프로세스가 자원을 점유하면 다른 프로세스는 해당 자원에 접근할 수 없다.
- No preemption
  - 현재 점유중인 자원을 다른 프로세스에서 요구 시 자원을 넘겨주지 않음.
- Hold and wait
  - 여러 자원을 요구하는것은 독립적이다. 특정 자원 점유 중 다른 자원이 추가로 필요한 경우 결과와 관계없이 현재 점유중인 자원은 계속 점유한다.
- Circular wait
  - 프로세스들이 서로가 점유중인 자원을 요구하는 경우(위의 resource allocation graph 상황).

**deadlock의 처리**

- deadlock 필요 조건을 하나라도 만족시키지 않으면 처리 가능
- mutual exclusion과 no preemption은 필수 조건
- **Hold and wait의 처리**
  - 프로세스가 필요한 자원을 한번에 점유하고, 하나라도 점유가 불가능 시 프로세스를 중단하고 자원의 점유를 해제
  - 실질적으로 프로세스가 어느 자원을 얼마나 필요로 할 지는 예측하기 힘들고 비경제적
- **Circular wait의 처리**
  - 프로세스들이 사용하는 리소스 각각에 번호를 부여하고 자원의  점유는 오름차순으로만 가능
  - 각각의 자원에 번호를 부여하기도 힘들 뿐더러 오름차순으로 점유하기도 힘들다.
- 두 경우 모두 현실적으로 문제가 있다.

**Solution**

- **Prevention**: 애초에 deadlock이 절대 발생하지 않도록 시스템을 구성
- **Detection and recovery**: deadlock 발생 시 deadlock 처리 과정을 수행



## Deadlock Prevention(Banker's Algorithm)

: deadlock의 필수 조건 중 하나를 방지

어떤 프로세스가 시스템 자원을 점유시 점유 상태를 2가지로 구분

- Safe state: 시스템이 deadlock에 빠지지 않는 상태. 시스템이 프로세스가 자원을 최대한 요구하더라도 수용할 수 있다.

- Unsafe state: 시스템이 deadlock에 빠질 가능성이 있는 상태

  **Key Idea**

**Key Idea**

- 프로세스는 필요한 자원의 최대 요구량을 선언해야 한다.
- 프로세스가 자원 요구 시, 알고리즘은 자원의 할당 시 여전히 시스템이 safe state 인지 확인한다.
- safe state가 유지된다면 프로세스에게 자원을 할당한다.
- 그렇지 않은 경우, 다른 프로세스가 자원 점유를 해제할때 까지 대기시킨다.

**data structure**

- m: 자원의 수
- n: 프로세스의 수
- Avail[1:m]: 특정 리소스 i가 현재 몇개나 가용한지 저장.
- Max[1:n, 1:m]: 특정 프로세스가 i에 대한 특정 리소스 j의 최대 요구량 저장하는 2차원 배열.
- Allocation[1:n, 1:m]: 프로세스 i에 대한 리소스 j의 현재 할당량.
- Need[1:n, 1:m]: Max - Allocation. 각각의 프로세스가 요구할 수 있는 자원의 양
- Work[1:m]: 특정 자원의 중간 계산 결과를 저장하고 있는 배열.
- Finish[1:n]: 특정 프로세스가 현재 종료되었는지를 나타내는 Boolean 배열.



## Deadlock Detection and Recovery

**Deadlock 처리 매커니즘의 한계**

- deadlock의 예측은 비효율적이며 expensive한 작업

**Deadlock Detection**

- binary semaphore: 이전의 그래프처럼 사이클의 존재를 확인하여 deadlock 탐지
- counting semaphore: banker's algorithm을 약간 변형한 detection algorithm으로 탐지

**Deadlock Recovery**

- 일반적인 데스크탑이나 PC의 경우 deadlock은 큰 문제가 아니다. Why? deadlock의 처리는 OS보다 User가 더 알기 쉽고 일반적으로 프로세스를 종료시켜서 처리
- 서버의 경우에도 이전의 알고리즘으로 deadlock을 확인하기 보다는 프로세스의 응답시간을 모니터링 하며 확인
- **CPR(Check Point and Resume)**: 애플리케이션 동작 중에 일정 시간 간격으로 애플리케이션의 모든 상태를 디스크에 저장하고, 문제가 발생했을 때 저장된 애플리케이션의 상태를 사용해 애플리케이션의 수행을 복구하는 기법. deadlock 발생 시 프로세스의 terminate 후 재시작은 비용이 크기 때문에 비용의 최소화를 위해 사용된다.
- 커널의 deadlock이 발생 하는 경우 
  - 일정 시간동안 커널이 반응하지 않으면 watchdog timer가 동작하여 커널을 종료시키고 재시작 한다.



