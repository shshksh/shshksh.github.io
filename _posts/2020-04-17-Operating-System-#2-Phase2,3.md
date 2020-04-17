---
title: "운영체제의 발전 - Phase 2,3"
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

# 운영체제의 발전 - Phase 2

## phase 2의 특징

- 하드웨어의 큰 발전. 트랜지스터와 집적 회로의 등장. 인건비의 상승.
- OS의 목적의 변화: 하드웨어의 효율성 -> 개발자의 생산성.
- 개발자 개인에게 **terminal** 을 제공하여 개인 컴퓨팅 경험을 제공.

**terminal**: 중앙 컴퓨터와 연결되어 데이터를 입력하거나 출력할 수 있는 장치.



### Terminal

- 여전히 비싼 하드웨어로 중앙 서버 컴퓨터는 여러대를 배치할 수 없다.
- 개발자들 개인에게 컴퓨터를 제공할 수는 없지만 서버 컴퓨터를 사용할 수 있도록 터미널을 제공.
- 실질적인 서버 컴퓨터는 1대이기 때문에 다수의 사용자에게 서버 CPU를 사용할 수 있는 시간이 분배되어야 한다. → **time sharing OS**
- 이에 따라 OS의 성능을 평가하는 척도가 추가되었다. Throughput(처리량) → response time(응답 시간)

phase 2: OS 학문 분야의 기반을 다진 시기.
<br>


# 운영체제의 발전 - Phase 3

keyword: **Connected**

## 특징

- 인터넷의 필수적인 사용. 네트워크 연결. 3 C convergence
- 하드웨어 가격의 하락, 복잡해지는 OS
- **OS with multimedia**
  - downloading vs streaming: 다운로딩의 경우 처리하려는 데이터를 모두 받은 후 작업 시작. 스트리밍의 경우 처리하려는 데이터의 일부만 가지고도 작업 시작.
  - 멀티미디어 처리를 위해 스케줄링 방식의 변화. Continuous media 처리.
- OS의 보편화 및 관심도 상승