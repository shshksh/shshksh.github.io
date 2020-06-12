---
title: "운영체제-19 Paged Segmentation"
excerpt: "OS L16 S1 - S3"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-06-11T13:10:00+09:00
toc: true
---

## 메모리 관리 방법의 발단

**초기의 프로세스**

- 한 job 이 **하나의** segment 를 받아 멀티 프로그래밍
- 문제점: 프로세스의 logical 한 unit 은 **여러개**(code, data, stack, heap)가 있고 각각의 세그먼트마다 read/write 등 다른 접근 권한으로 구분되야 하는데 이러한 작업을 효과적으로 할 수 없다.

**segmentation 도입**

- 한 프로세스를 **여러개의** 세그먼트로 분할
- 문제점: **세그먼트 테이블을** 작성하여 세그먼트별로 베이스와 바운드를 기록해야 한다. 메모리 참조에 **추가적인 메모리 연산이** 발생하여 성능이 저하된다. 세그먼트를 동적으로 할당할 때 각각의 세그먼트의 크기가 다르고 메모리 블럭이 연속적으로 할당되어야 하기 때문에 메모리 사이 사이에 **hole** 이 생성된다.(**fragmentation**)

**paging 도입**

- 할당하는 메모리 블럭의 사이즈를 **고정시키고** 메모리 블럭을 **분산할** 수 있다.
- 문제점: 메인 메모리 각각의 메모리 블럭마다 매핑되는 페이지 테이블을 가지고 있어야 하기 때문에 페이지 테이블의 크기가 **방대해진다는** 단점이 있다. 또한 segmentation 과 마찬가지로 메모리에 접근하기 위해 필요한 연산의 수가 증가한다.



# paged segmentation

**페이징의 문제점**

fragmentation 의 문제는 해결했지만 프로세스의 유닛이 구분되지 않는다는 초기 프로세스의 문제점은 해결되지 않는다.

**paged segmentation**

segmentation 과 paging 각각의 장점을 한꺼번에 얻기 위해 두 기법을 동시에 사용하는 기법. 이를 위해 먼저 segmentation 을 수행하고 각 segment 별로 paging 을 수행한다.

**paged segmentation의 이슈**

paging table , segment table 의 구성을 어떻게 변경해야 하는가?

- **segment table**: 세그먼트의 base 와 bound 를 기록한다. segment table 의 base address 대신 해당 segment 의 page table 위치를 저장시킨다.
- **page table**: frame 넘버와 bound 를 기록한다. 기존 bound 값 대신 해당 segment 의 page 개수를 저장시킨다.

**단점**

메모리 접근 시 추가적으로 2번의 접근이 더 발생하여 성능이 더 하락한다.

**메모리 주소 변환은 어떻게 이루어 지는가?**

메모리 주소를 변환하는것은 MMU 에 의해 HW 적으로 이루어진다. 하지만 프로세스의 문맥 전환이나 새로운 프로세스의 생성등이 일어날 때, 운영체제가 Base/Bound Register 및 segment/page table 들을 관리해야 하므로 운영체제에게 Trasparent 하지는 않는다.

**주소 변환 시 고려할 점**

- **space overhead**: pure paging 에 비해 space overhead 를 줄일 수 있다. 기존의 페이징은 사용하는 메모리에 상관 없이 메인 메모리의 모든 블럭에 대해 페이지 테이블에 엔트리를 추가해야 했지만 paged segmentation 은 세그먼트가 사용하는 페이지 만큼만 기록하면 된다.
- **performance overhead**: extra memory access 가 2회 추가된다.



**machine independent, machine dependent**

paged segmentation 시 여러 이점이 있지만 리눅스의 경우에는 순수 페이징을 사용한다.

운영체제의 코드를 크게 **machine independent code**, **machine dependent code** 로 구분할 수 있다. 여기서 말하는 machine 은 크게 CPU(마이크로) 아키텍쳐의 차이, 전체 시스템 관리의 차이, 입출력 장치의 차이로 구분할 수 있다. 운영체제에서 이러한 machine 에 대해 무관한 코드를 **machine independent code** 라고 한다.

운영체제의 portability 를 높여 다른 하드웨어에서도 동일한 운영체제를 사용하기 위해서는 운영체제의 machine independent code 의 비중을 높이는 것이 좋다.

컨텍스트 스위칭, 태스크 생성, 입출력 관리, 메모리 관리 등이 대표적인 **machine dependent** 이다. 

 

**page frame 의 속성**

- **mapped page**: 전통적인 page address translate 과정을 거친 메모리 접근 방법.
- **unmapped page**: physical memory 를 사용하여 별도의 memory address translation 없이 바로 접근 가능한 page. page table 등에 사용됨.
- **cached page**: 일반적인 페이지. 한번 특정 word 가 억세스 되면 빈 공간에 저장 하고 나중에 hit.
- **uncached page**: 캐싱을 하지 않음. DMA 의 대상 page 처럼 여러 프로세스에 의해 동시에 읽고 쓰기가 일어날 수 있는 경우(sharing) 사용.

page table 은 unmapped page 로 physical 메모리 영역에 contiguous 하게 존재한다. 하지만 시스템에 unmapped page 의 사이즈가 커진다면 컨텍스트 스위칭 등에 있어서 시스템의 부담이 커진다. 따라서 페이지 테이블의 사이즈를 줄이는것 뿐만 아니라 연속적으로 존재하는 페이지 테이블을 분산할 수 있는지 또한 주요 이슈이다.(VAX)



**large page table 의 문제 해결 조건**

- page table 의 크기를 줄임.
- 연속적인 메모리 공간 요구량을 줄임.

 

 **TLB(Translate Lookaside Buffer)**

- 테이블에 접근하기 위한 추가적인 메모리 참조는 프로그램의 성능을 두 배 내지 세 배 가까지 하락시킨다.
- 논리 주소에서 물리 주소로 변환하기 위해서는 페이지 테이블 룩업 과정이 필수적인데, 이 중 자주 룩업되는 페이지 엔트리를 캐시하면 성능을 끌어올릴 수 있다.
- **issue**: 적중률
- TLB 의 사이즈를 키우는 방법은 적중률을 높일 수 있지만 그만큼 비용이 상승한다. 하지만 64 ~ 128 개의 TLB 만으로도 98% 가량의 높은 적중률이 나타난다. why? **locality**
  - spatial locality(공간적 지역성)
  - temporal locality(시간적 지역성)
  - 어떤 페이지가 루프에 있다면 해당 페이지를 다시 참조할 가능성이 매우  높다.
- TLB란? 일종의 캐시



**캐시의 구현(컴퓨터 구조 참고)**

- direct mapped cache
- fully associated cache
- set associated cache



**TLB의 정보**

- physical frame number
- page number
- flag bits(valid, dirty bit, don't cache check ...)
- MIPS의 경우 PID(page id) 페이지 넘버와 함께 PID 를 추가적으로 사용해서 TLB 룩 업 에 사용하도록 TLB 설계 (transparent)