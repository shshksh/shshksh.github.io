---
title: "인터럽트 메커니즘"
excerpt: "OS L3 S2"
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

## interrupt 개요

- **HW mechanism**
  - CPU 외부에서 CPU가 알아야 하는  중요한 제어 신호를 전송하는 메커니즘
  - asynchronous
  - 인터럽트 발생시 CPU의 컨트롤이 ISR(Interrupt Service Routine)으로 넘어가고 인터럽트를 처리
- **SW mechanism**
  - 현재 수행하는 프로그램에 문제 발생시 문제 해결을 위한 function으로 분기
  - 각종 Exception 등이 이에 해당
  - synchronous
  - "Trap" 이라고도 불리며 코드 분석으로 확인 가능



## interrupt operation

**interrupt mechanism**

- HW 인터럽트를 위해선 인터럽트 신호를 수신하는 물리적 핀이 있어야 한다
- 입출력 컨트롤러, DMA 컨트롤러 같은 **interrupt source**가 해당 핀으로 인터럽트 신호를 보낸다

**인터럽트 동작 과정**

1. 현재 수행중인 명령어의 동작을 완료하고 PC값을 안전한 곳에 저장
2. 현재 수행중인 프로그램을 중단
3. IRQ(Interrupt ReQuest)를 통해 인터럽트 소스를 확인
4. IVT(Interrupt Vector Table)을 검색해서 ISR의 주소를 확인하고 수행

※ IVT에는 인터럽트 신호에 따른 인터럽트 핸들러가 저장되어 있다

인터럽트의 처리가 끝나면 저장해둔 PC로 복귀한다



## interrupt hardware

**PIC**(Programmable Interrupt Controller)

![PIC](/assets/images/pic.png)

- PIC의 출력 라인은 CPU와 연결
- PIC의 입력 라인은 인터럽트 소스와 연결되어 인터럽트 신호 수신
- PIC를 직렬 연결하면서 다수의 인터럽트 신호를 수신 가능
- **Programmabe**: 필요에 따라 인터럽트를 SW적으로 disable 시킬 수 있다(interrupt mask)
- interrupt mask의 주소는 메모리 특정 영역에 매핑되어 있고 해당 주소로 입출력을 통해 on/off
- PIC의 레지스터에 접근하여 IRQ를 알 수 있음

