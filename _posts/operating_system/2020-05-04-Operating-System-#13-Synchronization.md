---
title: "운영체제-13 Synchronization"
excerpt: "OS L10 S1"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-04T18:42:00+09:00
toc: true
---

## 개요

- **atomic operation**

  : 여러 단계로 더 이상 분할할 수 없는 연산.  데이터가 처리되는 중간 단계를 확인할 수 없으며, atomic operation은 담당하는 작업을 모두 수행하거나 모두 수행하지 않음을 보장한다.

- **critical section**

  : 오직 하나의 프로세스만 수행할 수 있는 코드 영역. critical section의 코드는 둘 이상의 프로세스에서 동시에 수행될 수 없다.

- **deadlock**

  : 둘 이상의 프로세스들이 더 이상 진행할 수 없어 멈춘 상태. 자원에 동시에 접근 하거나 각각의 프로세스가 다른 프로세스의 진행을 기다리며 대기하는 경우 발생한다.

- **livelock**

  : 둘 이상의 프로세스들이 다른 프로세스의 상태 변화에 따라 자신의 state transition만 수행하는 상태. 프로세스가 작동중이긴 하지만 크게 의미가 없는 작업을 반복한다.

- **mutual exclusion**

  : 주어진 시간에 오직 하나의 프로세스만 접근이 허용된다는 조건. 여러개의 프로세스가 동시에 접근할 경우 이 중 하나의 프로세스만을 선택하여 접근을 허용할 수 있어야 한다.

- **race condition**

  : 두 개 이상의 프로세스가 동기화 절차를 거치지 않고 동시에 자원에 접근하기 위해 경쟁함으로써 그 수행 결과를 예측할 수 없는 상황.

- **starvation**

  : 특정 프로세스가 수행 가능한 상태임에도 불구하고 오랜 시간 수행이 연기되는 상황. (ex: 낮은 우선순위의 프로세스가 계속 뒤로 밀려 레디큐에서 무기한 대기)



## Synchronization

- 프로세스들의 상호작용 시 발생. interacting process 간의 resource sharing.
- interacting의 목적
  - 자원 공유
  - 성능 향상(concurrency)
  - 모듈화
- 프로세스간의 자원 공유는 프로세스 상호작용을 야기한다.
- interacting process
  - resource와 state(data)를 공유
  - non-deterministic: 프로세스의 수행 흐름이 가변적이다.
  - irreproducible: 복잡한 디버깅



## Uncontrolled Task Interactions

프로세스간의 상호 작용이 동기화되지 않는다면 프로세스의 진행에 문제가 발생한다. 두 개의 프로세스가 read와 write를 수행 중 한 프로세스에서 값을 읽어 오기 전 다른 프로세스에서 write가 이루어진다면 기존의 프로세스는 원하는 값과 전혀 다른 값을 가져오게 된다. 

**⇒ non-reentrant code. 공유하는 자원에 대해 동기화가 필요하다. atomic operation.**

- **Reentrant code**(재진입 가능 코드)
  - 여러 프로세스에 의해 동시에 호출되거나, 이전 호출이 완료되기 전 반복 호출되어도 올바르게 수행되는 코드



## Task Reentrancy

atomic operation의 구현은 하드웨어적 support가 필요하다.

**sample code**

```c++
if(BufferIsAvail) {
	BufferIsAvail = false;
	UseBuffer();
	BufferIsAvail = true;
}
```

위의 코드는 reentrancy를 보장하지 못한다. 여러개의 프로세스가 BufferIsAvail을 false로 바꾸기 전 if 블록에 진입할 수 있다. ⇒ **race condition**

if 블록의 진입점에서 인터럽트를 금지하고 진출점에서 다시 허용함으로 if 블록을 쉽게 critical section으로 만들 수 있다. critical section 내부는 mutual exclusion을 만족하기 때문에 race condition이 발생하지 않는다. 하지만 인터럽트의 중단은 간편한 방법이면서 강력하기때문에 또다른 문제를 발생시킨다.