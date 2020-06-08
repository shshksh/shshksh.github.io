---
title: "운영체제-18 Segmentation and Paging"
excerpt: "OS L15 S1"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-06-5T22:26:00+09:00
toc: true
---

# Agenda

> Segmentation
>
> Paging
>
> Paged Segmentation
>
> Enhancing Mechanisms
>
> Case Studies
>
> Conclusion



# Segmentation

이전에 소개한 multi programmed batch monitor 를 생각하면 몇 가지 문제점이 있다.



## Problems

- 유저 프로세스가 메모리상 할당, 해제를 반복하며 Fragmentation 발생
- 유저 프로세스의 memory section 들이 하나의 memory segment 로 다뤄짐
  - 두 유저 프로세스가 동일한 코드(Text or Code section)를 공유하기 힘들다.
  - 각 memory section 들에게 다른 Read/Write 권한을 설정하기 힘들다.



## **Solutions**

**Multiple segments**

- 프로세스를 몇개의 메모리 영역으로 나눌 수 있다.
- 각각의 나눠진 영역마다 베이스 레지스터와 바운드 레지스터와 읽기 쓰기 권한을 설정하는 Flag bits 가 필요하다.
- 각각의 프로세스 별 메모리 섹션에 대한 정보가 segment table 에 기록된다. 이에따라 메모리에 대한 접근이 복잡해진다.



## Address Translations

**메모리 주소의 구분**

- CPU 가 생성하는 주소(logical address)
- MMU 가 변환시킨 주소(physical address)

세그먼트 테이블은 세그먼트 각각의 번호, base address(physical address) 와 bound address, Read/Write 속성 등이 저장되어 있다. MMU 는 세그먼트 확인을 위해 segment table base register 를 가지고 있다.

**STBR**: 세그먼트 테이블의 시작 주소를 가리키는 MMU 의 레지스터.



## 문제점

- 메모리에 접근하기 위해 2번의 연산이 필요하게 된다.
  - MMU 가 segment table 에 접근하여 entry 를 load
  - entry 의 base register 를 사용하여 실제 주소 접근
- 메모리 상에 segment 의 수가 늘어나면서 fragmentation 발생.



# Paging

**Fragmentation 의 원인**

프로세스가 요구하는 메모리의 사이즈가 제각각이다.

**paging**

프로세스가 요구하는 메모리 사이즈를 **page** 라고 하는 **unit size** 로 나누어 메모리를 할당하는 기법. fragmentation 을 피할 수 있고 메모리 관리가 단순해 진다.

**page**

Logical Address Space 를 일정한 크기로 나눈 메모리 블럭.

**frame**

Physical Address Space 를 일정한 크기로 나눈 메모리 블럭으로 page 와 크기가 동일하다.



## Solutions

**Paging**

- 전체 메모리를 page 라고 하는 일정한 크기의 블럭으로 나눈다. 일반적으로 512 to 16 Kb 의 크기를 가진다.
- 각각의 프로세스가 page table 에 정의된다.
- segmentation 과 비슷한 방식의 주소 변환이 이루어진다. (PTBR: Page Table Base Register)
- internal fragmentation 이라고 하여 메모리의 사이즈가 page 크기의 배수가 아닐 때 마지막 page 는 온전한 크기를 가지지 못하지만, page 의 사이즈가 작기 때문에 큰 문제가 되지 않는다.



**장점**

- fragmentation 의 회피
- 효과적인 스와핑
- 메모리 할당이 용이

 

**단점**

- 메모리 접근에 대한 추가적인 연산
- page table 의 크기가 방대해짐
- paging 이 fragmentation 문제는 해결할 수 있지만 프로세스에게 하나의 세그먼트가 부여된다는 문제가 해결되지는 않는다.





