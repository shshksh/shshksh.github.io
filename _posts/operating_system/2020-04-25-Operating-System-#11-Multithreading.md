---
title: "운영체제-11 Multithreading"
excerpt: "OS L7"
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

# Server architecture

## **Basic**

- 서버는 무한루프를 돌며 request queue를 검사
- request가 있다면 처히 후 루프 반복

## Architecture(처리 패턴)

**Iterative server**

- 리퀘스트 수신 시 서버가 직접 처리
- 단일 프로세스

**Concurrent server**

- 리퀘스트 수신 시 처리를 위한 worker를 fork하여 처리를 넘기고, 자신은 다른 리퀘스트를 수신
- 리퀘스트가 늘어나는 만큼 하위 프로세스를 생성하기 때문에 시스템 부담 상승(오버헤드) ⇒ multi thread



# Multi-threading

**concurrency**는 높이면서 execution unit을 생성하거나 수행시키는데 드는 **부담**을 줄이기 위해 등장

## Traditional process model

- **process**
  - context: 일종의 자원
  - thread of control: Thread or lightweight process
  - 멀티 스레드의 경우 context를 서로 공유할 수 있다.

## 특징

- 실행의 주체가 프로세스에서 스레드로 넘어가여 스레드가 CPU 사용시간을 할당 받는다.
- 프로세스와 마찬가지로 execution state(running, wait, ready...)를 가진다.
- 스레드가 수행중이지 않을땐 context를 보존한다.
- 스레드들은 프로세스에게 부여된 주소공간과 자원을 공유한다.

**Thread 구현을 위한 자료구조**

- Stack
- Thread ID
- TCB(Thread Control Block)

## 필요성

- 적은 비용으로 concurrency를 얻기 위해, 효율적인 병렬 프로그래밍
- massively parallel scientific programming 에서 발생하는 오버헤드를 줄이기 위해, 경제적
- 자원 공유
- 신속한 반응



# 구현

**구현 방식**

- User-Level
- Kernel-Level
- Combined UL/KL



## User-Level

**Key entity**

- **Thread**
  - 100% user-level entity
  - 커널은 스레드의 존재를 신경쓰지 않는다.
- **Thread library**
  - 유저 영역에서 스레드를 관리하기 위해 스케줄러, 디스패처 등이 유저 영억에 구현되야 한다.
  - system call과 마찬가지로 유저 영역에 함수로 구현되어 있고 함수들을 모아 라이브러리를 구성한다.
  - 라이브러리 구성
    - 스레드 생성 및 제거
    - 스레드 스케줄링
    - 스레드 context 보존 및 복구
    - 스레드간 통신 등 thread management

**특징**

- 애플리케이션은 스레드 라이브러리를 통해 스레드를 관리(system call 불필요하며 non-preemptive 스케줄링을 쉽게 구현)
- 커널은 스레드를 알지 못하고 프로세스의 동작을 관리
- 스레드가 blocking system call 호출 시
  - 전체 프로세스(모든 스레드)가 blocked
- OS가 스레드에게 직접 인터럽트를 전달할 수 없기 때문에 preemptive 스케줄링 불가능



## Kernel-Level

- 스레드의 생성, 관리를 모두 system call로 구현
- User-level 문제를 해결할 가능(preemptive scheduling)
- 커널 영역 수행 시간이 증가하면서 오버헤드 상승



## Combined UL/KL

- 유저 레벨에서 preemptive 스케줄링 문제를 해결할 수 있도록 기능 추가
- 대표적으로 Solaris 
- 커널 영역 스레드와 유저 영역 스레드를 연결(N : N)하여 관리 가능



# Pthreads programming model

- **POSIX(Portable Operating System Interface)**

  - 다양한 UNIX 계열 운영체제들의 API를 표준화 하기 위해 IEEE가 정의한 인터페이스

- **Pthread**

  - POSIX에서 multi-threading에 관련된 API
  - Pthread APIs
    - pthread_cread(): create a new thread
    - pthread_exit(): terminate the calling thread
    - pthread_join(): wait for a specific thread to exit
    - pthread_yield(): release CPU to let another thread run

  