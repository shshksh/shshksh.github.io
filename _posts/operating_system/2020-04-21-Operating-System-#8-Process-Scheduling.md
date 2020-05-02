---
title: "프로세스 스케줄링"
excerpt: "OS L4 S3, L5 S1"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-04-21T18:35:00+09:00
toc: true
---

# 개요

**OS의 목적**

- 각 프로세스들이 공평하게 CPU를 점유하기 위해 OS는 다음 실행할 프로세스를 선택해야 한다.



**제약 조건**

- **Fair**
- **CPU protection**



# Scheduler design principle

**separation of policy and mechanism**

- **Policy**: 다음에 수행될 프로세스를 선택하는 기준(scheduling policy)
- **Mechanism**: CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 방법(dispatcher)



# Dispatcher

**what is dispatcher**

- 다음 프로세스를 수행하는 OS의 최심부

- ```
  loop forever {
  	run the process for a while
  	stop it and save its state
  	load state of another process
  }
  ```

- user program 수행 중 개입하여 CPU의 사용 권한을 이전

- 권한 이전을 위해서는 CPU를 점유해야 한다

- 즉 CPU의 점유는 user program → dispatcher(os) → another program



# 커널

**커널에 대한 오개념**

- 커널은 능동적인 주체가 아니다. 즉 커널은 커널 모드에서 수행되는 함수들의 collection.



**System call**

- 운영 체제의 커널이 제공하는 서비스를 응용 프로그램에서 요청하기 위한 인터페이스
- 커널 함수는 system call을 구현하는 함수와 ISR(Interrupt service routine)로 구성되어 있다.



**Kernel space vs User space**

- **커널 모드**
  - 유저 모드에 비해 강력한 system state
  - 하드웨어에 대한 완전한 접근 가능
- **유저 모드**
  - 커널 모드에 비해 제한된 system state
  - 가용한 자원의 일부
  - privileged에 대한 제한
- 프로세서는 PSW(Process State Word) 레지스터의 특정 bit(mode bit)를 사용하여 모드를 구분한다



**Dispatcher를 수행하는 방법**

- **Non-preemptive scheduling**
  - 프로세스가 자발적으로 CPU를 양보하여 다른 프로세스를 수행하는 스케줄링
  - SW적 인터럽트(a.k.a Trap)에 의해 촉발
- **Preemptive scheduling**
  - OS가 강제로 프로세스로부터 CPU를 빼앗아 다른 프로세스를 수행하는 스케줄링(CPU protection)
  - HW적 인터럽트에 의해 촉발



**Trap의 예**

- 유저 프로그램 작동 중 커널 함수(메모리, I/O, ...)가 필요 하다면 Trap을 통한 System call을 사용한다.
- 파일 입출력을 위해 read syscall을 호출 시 실제로 read의 동작은 파일을 읽는것이 아니라 인터럽트를 발생시킨다.
- 인터럽트의 인자로 read라는 함수에 대한 정보를 전달하고 Trap에 의해 커널 모드로 변경되어 system call 함수인 sysread를 수행하고 결과를 반환한다.
- 커널 모드 execution이 발생한 상황에서 수행의 주체가 되는 프로세스는 여전히 system call을 호출한 user process이다.



**Process**

- 프로세스의 요소
  - **State**(as Context)
  - **Thread of control**
- 어떤 프로세스의 실행 상황은 **Stack**을 통해 확인할 수 있다.
- Stack은 해당 프로세스의 run-time entity
- 프로세스가 user mode에서 수행될 때는 user mode stack을 사용하고, 커널 모드에서 수행될 때는 kernel mode stack을 사용한다. ⇒ **run-time context의 변화**
- run-time context가 변화하더라도 수행의 주체는 여전히 user process이다.



**System call vs Function call**

- 두 호출 모두 한 루틴에서 다른 루틴으로 분기할 때 호출된다.
- 하지만 system call의 경우 모드 체인지가 발생한다.