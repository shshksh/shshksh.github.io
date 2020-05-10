---
title: "운영체제-9 Context switching"
excerpt: "OS L5 S2, L6 S1"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-04-21T19:00:00+09:00
toc: true
---

# 개요

**Context switching**

- 현재 수행중인 프로세스의 state를 저장하고 다음 수행될 프로세스의 state를 불러오는 작업



**Context saving**

- CPU register data(HW context): 다음 수행하는 프로세스가 레지스터를 사용한다면 해당 레지스터의 값을 보존해야 한다.
- Memory: 메모리의 공간이 부족했던 uniprogramming의 경우 메모리의 모든 값을 따로 저장해야 했지만 지금은 필요한 경우 부분적으로 보존한다.(swapping)
- System context: 기본적으로 PCB에 기록되어 있기때문에 따로 저장할 필요가 없다.



**Q: CPU의 레지스터 값은 disk로 들어가는가?**

- 일반적으로 데이터의 보존은 memory hierarchy에서 다음 단계로 가기때문에 CPU 레지스터의 값은 메인 메모리에 저장된다.



# Mechanism

- 다음에 수행할 명령어가 들어있는 PC(Program Counter)와 이전의 mode bit의 값을 스택에 저장하고 다시 프로세스로 복귀할 때 저장한 값을 복구한다(HW적 support)
- 인터럽트 발생시 micro processor가 pc와 psw를 스택에 푸시하고(데이터 보존) 이후 ISR로 들어간다.
- ISR는 최우선적으로 현재 CPU 레지스터 값을 보존해야 한다(스택에 저장) 이후 인터럽트 처리를 위한 동작을 수행한다.



# Abstraction and Decomposition

- **API**: Layer 사이의 통신을 위해 정의된 호출 규약(OS의 경우 system call)
- **Abstraction**: 하위 계층의 복잡함을 추상화하여 단순한 인터페이스를 상위 계층에 제공하는것
  - layering principle: 상위 계층은 하위 계층이 제공하는 기능만을 사용할 수 있으며 반대의 경우는 발생하지 않아야 한다.