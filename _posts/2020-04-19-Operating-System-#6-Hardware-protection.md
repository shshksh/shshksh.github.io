---
title: "하드웨어 프로텍션 메커니즘"
excerpt: "OS L3 S3"
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

## 개요

- **Privileged instruction**: OS만 수행할 수 있는 명령어



## Dual mode operation

**목적**

- 멀티 프로그래밍의 경우 한 프로그램이 다른 프로그램을 침범하는 행위를 막아야 한다
- 2가지 이상의 모드를 통해 실행할 수 있는 명령어를 구분한다



**Dual mode operation**

- 마이크로 프로세서의 특정 레지스터(status register)가 특정 모드를 결정한다
- 정확히는 레지스터가 여러가지 선행 수행된 결과를 기록한 flag bit로 구성되었고 이중 한 비트가 mode bit
- 커널 모드(0)와 유저 모드(1)로 모드가 결정된다
- 커널 모드의 경우 privileged instruction을 수행 가능하고 유저 모드의 경우 명령어 수행이 불가능하다
- 컴퓨터 시스템은 기본적으로 user program을 신뢰하지 않고 OS를 신뢰한다



**OS가 커널 모드에서 수행될 때 부여받는 주요 권한**

- privileged instruction을 수행 가능
- 모든 메모리 영역에 접근 가능(user program은 할당받은 메모리만 접근 가능)



**Q: 어떻게 모드 체인지가 통제되는가?**

- 모드 변경을 커널 모드에서 동작중인 OS가 관장하도록. 유저 프로그램에서 privileged instruction 수행이 필요하면 인터럽트를 발생시켜 모드를 변경. 하드웨어적으로 mode bit 변경.



## I/O protection

- I/O device 나 메모리는 자원이다. 어떤 job이 자원을 마음대로 사용할 수 있다면(독점할 수 있다면) 자원을 효율적으로 사용할 수 없다. 따라서 자원에 대한 **protection**이 필요하다.
- 입출력에 접근하는 명령어를 privileged instruction으로 하여 protection할 수 있다. 즉 I/O와 관련된 모든 함수들은 커널 내부로 들어가야함을 알 수 있다.



## CPU protection

- CPU 또한 자원으로 취급되며 당연히 독점할 수 없다
- job별로 CPU를 사용할 수 있는 시간을 분배하여 자원을 공유한다