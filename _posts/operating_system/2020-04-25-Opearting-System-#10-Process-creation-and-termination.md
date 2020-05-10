---
title: "운영체제-10 Process creation and termination"
excerpt: "OS L6 S2, S3"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-04-25T21:45:00+09:00
toc: true
---

# Process creation

- **프로세스 생성 과정**
  1. execution file 의 명령어(code segment)와 전역 변수, 정적 변수(data segment)를 메모리에 load
  2. call stack 과 heap 생성
  3. PCB 생성 및 초기화
  4. ready queue에 PCB 추가
- UNIX 의 경우 0번(초기) 프로세스만 위의 과정으로 생성하고, 이후엔 **cloning** 을 통해 프로세스 생성
  - ***fork()***: cloning 을 위한 **UNIX system call**
- UNIX 에서 프로세스 생성은 프로세스의 생성을 명령하는 다른 프로세스가 있어야 한다.
  - **parent process**: process cloning 을 초래하는 기존의 process
  - **child process**: parent process 로 부터 생성되는 새로운 process
  - parent process 의 *fork()* 호출 ⇒ child process 생성



## Cloning

**cloning 과정**

1. *fork()* 호출 시 parent process 일시 중단
2. parent process context 의 snapshot을 찍어 그대로 복사(레지스터 값, 포인터 등 데이터의 복사)
3. 새로운 PCB 를 ready queue 에 추가 후 parent process 로 복귀

**문제점**

- 매번 모든 context를 모두 복사하는것은 비효율적인 작업
- 처음 만든 프로세스와 다른 프로세스는 만들 수 없다

**추가 system call**

- ***exec()***: 복사한 데이터에 새로운 데이터를 overwrite
- ***exit()***: 프로세스 종료 명령, 모든 프로세스는 정상 종료시 exit() 호출

**parent process와 child process 생성의 흐름**

- child process 동작시 parent process 동작 중단(wait() 호출), child process가 exit()을 호출하고 exit status를 parent process에게 전달, parent process는 exit status를 확인 후 자신의 작업 수행

**Zombie state**

- exit()을 마치고 parent process가 자신의 exit status를 읽어가기를 기다리는 process state

**COW(Copy On Write)**

- process context를 fork한 시점에서 복사하지 않고, data segment에 새 값이 쓰여질 때 복사하는 기법
- 현재의 OS는 fork로 발생하는 오버헤드를 거의 신경쓰지 않도록 구성됨



## Process termination

- **exit()**: 프로세스가 정상적으로 작업을 마치고 종료를 위해 exit()을 호출
  - parent process의 wait()를 통해 출력 데이터(exit statue)가 parent process로 전달됨
  - 프로세스가 가진 자원들이 OS에 의해 반환된다(deallocated)
- **abort()**: parent process가 child process의 실행을 강제로 종료
  - child process가 자신에게 할당된 자원을 초과하거나
  - child process의 작업이 더 이상 불필요 하거나
  - parent process가 종료된 경우