---
title: "운영체제-3 운영체제의 기능"
classes: wide
categories: 
  - OS
tag:
  - OS
  - operating system
read_time: false
last_modified_at: 2020-04-17T23:08:00+09:00
toc: true
---

## 현대 OS의 특징

- **Large**: 1000만 라인 이상의 소스 코드, 100 ~ 1000 man-year의 작업
- **Complex**: 비동기 작업의 처리, 복잡해지는 하드웨어, 다양한 사용자등의 요구사항의 충돌 등의 문제를 해결하기위해 OS는 점점 더 복잡해짐
- **Poorly understood**: 초기 OS 개발자의 이직 후에도 시스템은 여전히 동작한다. 전체적인 디버깅이 매우 복잡하며 OS의 다음 행동을 예측하기 힘들다.



## OS의 기능

- **Coordinator**: 여러 task의 요구사항을 만족하면서 충돌 업싱 작동하도록 중재자 역할
- **Illusion generator**: 하드웨어에 대한 고 수준의 인터페이스를 제공
- **Standard library**: 다수가 필요로 하는 표준 함수들을 제공



## Coordinator

**목적**: 많은 작업이 문제 없이 동시에 진행되도록 중재

- Concurrency(동시성): 스케줄링
- I/O device manager
- Memory manager
- 추가로 file system, network



**입출력**

- **char I/O device**: byte 단위로 입출력(키보드, 마우스 ...)
- **block I/O device**: block 단위로 입출력(HDD...)
- **network I/O device**: network controller(socket)



단순한 char I/O device 외에도 별도의 시스템을 만들어서 사용할 만큼 입출력은 중요하다. 입출력을 어떻게 관리하느냐에 따라서 성능에 큰 영향을 끼친다.



### memory

- 메모리는 레지스터, 캐시 메모리, 메인 메모리, 하드 디스크 등 여러 계층으로 구성되어 있다(memory hierarchy).
- 대체로 레지스터에 가까울수록 저용량, 고성능이며 하드디스크에 가까울수록 대용량, 저성능이다.
- OS가 캐싱을 제공함으로 극적인 성능 향상을 보여준다.



## Illusion generator

- 복잡한 시스템(하드웨어)을 사용자가 간단히 사용할 수 있도록 추상화를 제공(Time sharing, Virtual memory)
- **Thrashing** 등의 문제가 발생할 수 있다.
  - Thrasing: 제공 하는 기능이 어느 임계점(Thrashold)을 넘으면 응답 시간이 급격히 상승.



