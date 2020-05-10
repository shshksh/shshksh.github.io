---
title: "운영체제-7 프로세스"
excerpt: "OS L4 S1, S2"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-04-19T18:35:00+09:00
toc: true
---

## 프로세스 개요

- **process**: OS상에서 프로그램을 실행하는 기본 주체. run-time system 수행 주체. 자원을 할당받는 주체. decomposition의 한 유닛.
  - **decomposition**: 복잡한 문제를 해결할 수 있는 간단한 문제로 나누어 해결하는 방법론
- 복잡성을 해결하는 방법
  - **추상화**
  - **decomposition**



## 프로세스

**정의**

- program in execution
- 특정 process state 위에서 수행되는 execution stream



**program**

- 저장매체에 저장된 수동적인 코드 시퀀스(Storage만 필요)

- 프로그램을 실행하면 메모리 등의 자원을 할당 받는다 => **Process**



**process state**: 프로세스가 수행되는데 또는 수행의 결과로 영향을 받는 정보들의 집합

1. memory context
   - code segment, data segment, stack, heap
2. HW context
   - register value
3. system context
   - 프로세스 각각의 정보, 커널이 관리하는 정보

**execution stream**: 프로세스가 실행해야하는 명령어의 순서, 흐름



## Multiprogramming vs Multiprocessing

**Multiprogramming**

- 메모리 관점
- 메모리 내부에 여러개의 active job이 존재

**Multiprocessing**

- CPU 관점
- 하나의 CPU가 여러개의 process에 이해 multiplexing
- 예전에는 메모리가 부족하여 uniprogramming으로 동작함

**Swapping**

- 메모리 부족 문제를 해결하기 위해 CPU를 사용하지 않는 프로세스의 데이터를 메모리에서 다른 저장장치로 내보내고, CPU를 사용할 프로세스의 데이터를 메모리에 로드하는것



## Process of "Design-time entity" vs "Run-time entity"

**SW System 개발**

1. **설계**
   - 요구사항 ⇒ [설계] ⇒ {Tasks}
   - **Task**: 설계하는 시스템의 한 부분을 독립적으로 구성하는 요소, **design-time entity**
2. **구현**
   - {Tasks} ⇒ [구현] ⇒ {Programs} ⇒ Processes
   - Process는 CPU 스케줄링, 자원 분배의 대상으로 **run-time entity**
   - 구현이란 design-time entity를 run-time entity로 **매핑** 하는 행위



## Process as "Data structure" + "Algorithm"

**프로세스의 자료구조** = **process state**

**System context**

- OS는 각각의 process에 대한 정보를 가지고 있다
- 해당 정보는 PCB(Process Control Block)에 담겨져 있다
- **PCB의 정보**
  - 프로세스 ID
  - 스케줄링 등 자원할당에 관련된 정보
  - 관리를 위해 시스템 전역에 걸친 PCB collection ⇒ **Process table**



## **State transition**

**프로세스 생명 주기**

- 프로세스가 생성되었을 때 부터 종료될 때 까지 발생하는 일련의 상태 변화

![process life cycle](/assets/images/process-life-cycle.jpg)

- **ready**: active하지만 아직 CPU를 할당받지 않음
- **running**: CPU를 할당받아 실행 중
- **waiting**: 입출력 등의 인터럽트가 발생하여 이벤트를 대기하며 준비중. 완료 신호를 수신하면 ready로 변경. 각각의 수신을 기다리는 이벤트 별로 다른 waiting queue가 있음

**※** ready queue

- ready 상태의 프로세스들을 관리하기 위한 queue
- 일반적으로 PCB들을 linked list로 연결하여 구현

