---
title: "운영체제-12 Scheduling"
excerpt: "OS L8 S2 - L9"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-01T22:12:00+09:00
toc: true
---

## 개요

**리소스**

- preemptible: 한 프로세스가 점유한 상태에서 다른 프로세스에게 양보할 수 있는 자원(ex. CPU)
- non-preemptible: 한 프로세스가 점유하면 사용을 마칠때까지 다른 프로세스에게 양보할 수 없는 자원

**리소스 할당 이슈**

- 다음 자원은 누구에게 할당되는가?
- 자원을 얼마나 오래 점유하는가?

**Job(batch monitor)**

- 한번 수행이 시작되면 완료될 때 까지 계속 수행되는 작업

**CPU Burst**

- 모던 OS에서 CPU 스케줄링의 단위(job → cpu burst)
- 프로그램의 수행 중에 연속적으로 CPU를 사용하는 단절된 구간

**I/O Burst**

- 프로그램의 수행 중에 I/O의 완료를 기다리며 블록되는 구간

**CPU Scheduler**

- CPU 할당 이슈를 처리(다음 할당받을 주체, 점유 시간)
- running → ready or wait → ready 의 경우 preemptive scheduling
- running → waiting, terminate 의 경우 non-preemptive scheduling



## Scheduling 목표

- 리소스 사용성의 최대화(CPU와 I/O 장치를 Idle 상태를 가능한 최소화)
- 오버헤드의 최소화
- Context switching의 최소화
- CPU cycle의 공평한 분배
  - 단순히 우선순위에 기반해여 할당시 문제 발생
  - aging: task가 cpu를 기다리는 시간에 비례하여 우선순위를 점차적으로 높이는 기법
- Thoughput의 최대화
  - 단위 시간당 처리 완료되는 프로세스의 수
- Turnaround time의 최소화
  - 특정 프로세스를 수행하는데 필요한 시간의 합
- Waiting time의 최소화
  - 프로세스가 ready queue에서 대기하는 시간
- Response time의 최소화
  - 프로세스가 첫 번째 응답을 보내는데 걸린 시간



## FIFO(First In First Out)

- 선입선출
- 단순하며 직관적
- 스케줄링의 단위는 CPU Burst
- 문제점
  - 어느 프로세스가 CPU를 독점할 수 있다.(CPU burst 내부 무한루프)
  - Context switching 없이 프로세스가 CPU를 점유할 수 있는 최대 시간을 제한하여 해결할 수 있다.(RR)



## SJF(Shortest Job First)

- 가장 CPU burst 가 적은것 부터 수행
- optimal, average waiting time이 최소
- 문제점
  - 운영체제가 태스크의 다음 CPU burst size를 예측할 수 없다
- variation
  - non-preemptive
    - CPU가 프로세스에게 할당 되면 해당 프로세스의 CPU burst가 완료될 때 까지 계속 수행
  - preemptive
    - 프로세스 수행중 CPU burst가 더 적은 프로세스가 들어온다면 rescheduling
    - 스케줄링의 시점: 현재 수행중인 태스크가 종료될 때, 새로운 태스크가 생성되거나 깨어날 때

**과제**

- 다음 CPU burst의 사이즈를 예측할 수 있어야 한다 ⇒ exponential average

**exponential average**

- 이전에 실제로 측정된 burst length를 고려하여 다음 burst length를 예측
- 실제로 적용하긴 힘들다



## RR(Round Robin)

- 기본적으로 FIFO 스케줄링
- **time slice**: x = 태스크에게 CPU가 할당된 후 다음 스케줄링을 위한 timer interrupt가 발생할 때 까지의 시간
- 프로세스는 time slice가 끝나면 다시 ready queue에 push
- 현대 OS는 대부분 100ms의 time slice
- 항상 FIFO보다 뛰어난 성능을 보여주지는 않는다
- burst size가 균일한 경우 FIFO가 좋은 성능을 보여주고 size가 다양하다면 RR이 좋은 성능을 보여준다
- 따라서 burst size에 따른 가변적 스케줄링이 필요하다 ⇒ adaptive scheduling
- CPU bound process와 I/O bound process 에 따라 다른 스케줄링 기법이 적용되야한다
- CPU bound process: 높은 CPU utilization, Throughput
- I/O bound process: 낮은 CPU utilization, Response time



## Multi-level Feedback Queue

- running queue를 계층적으로 여러개를 생성한다.
- 최 상단의 큐는 높은 우선순위와 작은 time slice를 가진다
- 새로 생성되는 프로세스는 최 상단 큐에 들어가며 time slice 내에 작업을 끝내지 못하면 하위 큐로 강등된다
- exponential queue scheduling: 하위 큐로 내려갈수록 time slice가 지수적으로 증가



## Fair share scheduling

- Real time system: 작업 수행이 요청되었을 때, 이를 주어진 시간 안에 반드시 성공적으로 마쳐야 하는 시스템
- priority-based scheduling
  - 프로세스들에게 선호도를 매기고, 선호도가 높은 프로세스를 먼저 수행하는 스케줄링 기법
- starvation(우선순위 기법의 문제)
  - Ready queue에서 대기중인 프로세스가 무기한 연기되는 현상
- fair share scheduling
  - 대기중인 프로세스들에게 정해진 비율에 따라 CPU의 Bandwidth를 분배하는 스케줄링 기법