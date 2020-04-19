---
title: "컴퓨터 시스템 아키텍쳐"
excerpt: "OS L3 S1"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-04-19T18:00:00+09:00
toc: true        
---

## Computer System 개요
![computer-system](/assets/images/computer-system.jpg)
- **I/O controller**: CPU를 대신하여 입출력을 관장
- **System Bus**: CPU, memory, I/O device 간 데이터 전송을 담당
    - **data bus**: bus master와 bus slave 사이의 데이터 전송 버스
    - **address bus**: 주소 정보가 전달되는 버스
- 버스를 통해 **read**와 **write**가 가능
- **Bus transaction**: read or write transaction



## 시스템 상호 연결

**bus master**

- 버스 트랜잭션을 시작하는 주체. 대표적으로 CPU, I/O controller, DMA controller 등이 있다
- 버스 트랜잭션을 시작할때는 최 우선적으로 버스를 장악해야 한다.

**bus arbiter**

- 여러 버스 마스터들이 서로 버스를 장악하려 할 때, 특정 버스 마스터에게 버스를 제공하는 **조정자**.
- **조정 과정**
    - 버스 마스터로부터 bus request signal 수신
    - 버스 마스터에게 grant signal을 응답함으로 버스를 제공

**bus slave**
- 버스 마스터로부터 명령을 받아 요구사항을 마스터에게 제공
- <u>데이터를 담고 있는 장치</u>
- <u>주소를 가지고 있음</u>
- ex) 메모리 컨트롤러, (CPU가 입출력 주관시)입출력 장치

---

**Q**: 입출력 장치도 데이터를 가지고 있는가?

- YES, 정확피는 내부 buffer 메모리와 register가 slave

**Q**: 메모리의 주소와 입출력 장치 내부 메모리의 주소는 달라야 하는가?
- 구현 방식에 따라 같게 할 수도, 다르게 할 수도 있다.



## I/O operation

**I/O device controller**

- 내부 버퍼로 데이터를 읽어들이거나 입출력 장치로 데이터를 출력
- 수행하는 작업(read or write)이 완료되면 CPU에게 완료 신호를 보낸다(interrupt)

**Interrupt-driven I/O**
- 입출력 동작 완료시 입출력 컨트롤러가 CPU에게 비동기적으로 완료 되었음을 알려주는 방식

**Polling I/O**
- 입출력 동작이 완료될때 까지 CPU가 반복해서 입출력 컨트롤러의 상태를 확인하는 방식  



**I/O addressing**: 입출력 장치의 메모리에 접근하는 방법
- **port-mapped I/O**
    - device register를 위해 별도의 주소를 부여(일반 메모리 주소와 구분된다)
    - 입출력을 위해 별도의 명령어가 필요하다
- **memory-mapped I/O**
    - 메모리 주소공간의 일부를 할애하여 입출력 장치 레지스터에게 부여
    - 별도의 명령억 불필요하다
    



## DMA(direct memory access)

- CPU가 DMA controller에게 block의 시작 주소, 크기, opcode를 제공하여 입출력 시작을 요청
- 이후의 데이터 전송 작업은 DMA 컨트롤러가 관장
- CPU의 개입 없이 입출력 컨트롤러가 <u>메모리에 직접 접근</u>(DMA)하여 데이터를 읽거나 쓸 수 있도록 하는 기능
- block 데이터 전송시 CPU intervention을 최소화 하기 위해 사용
- 데이터를 전송하는 방법은 2가지로 나뉨
    - **cycle stealing**: CPU가 버스를 사용하고 있지 않을때만 DMA 컨트롤러가 버스를 장악하여 데이터 전송
    - **block transfer**: CPU와 DMA 컨트롤러가 대등하게 경쟁하여 버스를 사용 