---
title: "운영체제-17 Dynamic Allocation"
excerpt: "OS L14 S1 - S2"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-24T20:00:00+09:00
toc: true
---

## 개요

**Activation Records(Stack Frames or Activiaton Frames)**

- 프로시저가 수행되기 위해 필요한 정보들을 저장하고 관리하기 위해 스택에 저장되는 데이터 구조
- 프로시저의 시작부터 끝 까지의 라이프 사이클을 가진다. 따라서 프로시저의 호출과 종료는 스택의 push 와 pop 과 대응된다.



## Static vs Dynamic

computer science 에서 static 과 dynamic 은  execution 전, 후로 구분한다.

**static X**

- X는 pre-runtime 에서 동작한다(or offline).
- X에는 analysis, synthesis, allocation, scheduling 등이 해당
- static allocation: 프로그램이 수행되기 전에 메모리를 할당 받음. 메모리 세그먼트에서 Stack 과 Heap segment 는 프로그램의 실행 중에 할당하는 메모리, 지역 변수 등이 담기므로 런타임상에서 동적으로 allocation 된다는 것을 쉽게 알 수 있다. 하지만 Code, Data 등의 세그먼트는 프로그램의 수행 전 링커가 모듈들을 통합하고 각각에 메모리 주소 등의 컨텍스트가 할당 되어야 한다. 이러한 static allocation 은 변수의 라이프 사이클이 프로그램의 시작과 종료와 일치한다.

**Dynamic X**

- X는 런타임 상에서 동작한다.



## Why Dynamic Allocation?

그렇다면 프로그램의 수행 전 필요한 모든 것 들이 allocation 되어 있다면 굳이 런타임상에서 allocation 할 필요가 없지 않을까? ⇒ **Unpredictability**. 메모리를 얼마나 사용할 지 예측하는데 충분하지 않다.

대표적으로 함수의 calling sequence, 복잡한 자료구조 등에 대한 할당은 사전에 사용량을 예측하기 힘들다.



## Dynamic Allocation

**대표적인 방법**

- Stack allocation
  - 제약적인 구조. 연산은 항상 스택의 탑에서만 일어나기 때문에 제약이 강하지만 특정한 상황에서는 효율적인 구조.
- Heap allocation
  - 단순히 메모리의 할당과 해제에 대한 2가지 기능을 사용한다.



## Heap Organization

on demand 로 필요한 양만 메모리를 할당하고 사용이 끝나면 해제할 수 있기때문에 효율적. 

**heap**

- big chunk of memory with memory allocating operation

**메모리 할당 과정**

- 초기에는 힙에서 순차적으로 메모리를 할당(malloc or new)
- 프로세스가 수행되며 사용이 끝난 메모리는 할당받은 메모리를 해제 ⇒ 힙의 사이 사이 빈 공간(가용메모리)이 생긴다(free or delete)
- 힙의 빈 공간들을 포인팅하여 연결리스트를 구성 ⇒ Free List

**Free List**

- 힙의 사용 가능한 공간들을 연결한 리스트

**Free 함수의 역할**

- Free List로 반환되는 공간이 기존 가용한 공간과 병합 가능한 경우 하나의 큰 공간으로 병합하고 아닌 경우 새로운 포인터를 추가

**Problem**

- 메모리를 효율적으로 사용할 수 는 있지만 `malloc` 과 `free` 가 빈번하게 발생함에 따라 `hole` 의 크기는 점점 작아지고 많아지게 된다.

**Fragmentation**

- 사용하기에는 너무 작은 홀 때문에 메모리의 비효율적인 사용으로 인해 발생
- 첫 번째 해결책: 프로세스가 요구하는 메모리를 한번에 할당해줄 수 없다면 프로세스를 슬립시키고 다른 프로세스가 메모리를 반환하여 합병될 때 까지 대기 ⇒ 서로 메모리 해제를 기다리며 **데드락에** 빠질 수 있다.



**Buddy allocator**

메모리의 총량은 2의 지수승. 메모리를 할당해줄 때 메모리를 2로 나눠가며 요구량보다 크면서 가장 작은 2의 지수승(Buddy) 만큼의 용량을 할당해준다.

**Buddy 에서의 Free**

인접한 Buddy 들이 가용 상태인 경우 이들을 병합함으로써 쉽게 큰 크기의 가용한 메모리 공간을 확보할 수 있다. 



## Free List

**Free List 를 관리하는 방법**

- **best-fit**: free list 를 탐색하여 메모리의 요구량에 가장 가까운 청크를 할당
  - 시간이 지남에 따라 first-fit, worst-fit 에 비해 작은 크기의 hole 들이 생성되기 때문에 fragmentation 문재가 더 심해질 수 있다.
- **first-fit**: free list 를 탐색하여 요구량을 만족하는 청크가 있다면 바로 할당
- **worst-fit**: 리스트를 탐색하며 가장 큰 청크를 할당(hole의 크기가 크다)
- 각각의 방법이 최선이 아니며 장단점이 있기 때문에 스케줄링과 달리 free list를 관리하는것은 policy 만으로는 불충분하다.