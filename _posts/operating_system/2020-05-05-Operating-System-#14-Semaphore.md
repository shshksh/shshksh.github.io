---
title: "운영체제-14 Semaphore"
excerpt: "OS L10 S2 - L11 S2"
classes: wide
categories:
    - OS
tags:
    - OS
    - operating system
read_time: false
last_modified_at: 2020-05-05T23:35:00+09:00
toc: true
---

## 개요

단일 프로세서에서 interacting 하는 프로세스들은 공유 자원에 순차적으로 접근해야 한다.

- **Semaphore**
  - Synchronization을 제공해 주는 정수형 변수

프로세스들은 세마포어를 확인함으로 자원이 사용중인지 판단할 수 있다.

- **mutex(mutual exclusion lock)**
  - mutual exclusion을 구현하기 위해 사용하는 sleeping lock
  - 객체를 얻거나 반납할 때 사용하는 프로그래밍 플래그
  - 세마포어와의 차이점은 동일한 mutex를 사용할 때 mutex_lock() 을 호출한 프로세스와 mutex_unlock() 을 호출한 프로세스가 일치해야 한다. 즉, 락을 설정한 프로세스만 락을 해제할 수 있다.



## Semaphore

**API** (S: Semaphore)

- lock(S), unlock(S): Synchronization 관점
- wait(S), signal(S): 스케줄링 관점
- P(S), V(S)

세마포어의 operation은 atomic operation 으로 수행시 올바른 결과를 보장한다.

세마포어는 동기화를 위해 사용되지만 실제로는 스케줄링에도 영향을 끼친다.



**세마포어의 장점**

Synchronization을 위해 interrput disable을 사용하면, 하나의 태스크에서 interrupt disable을 호출하여도 전체 시스템에 영향을 끼치므로 프로세스의 수행을 방해한다.

**단점**

Unstructed Programming Construct 이므로 컴파일러 등의 도구를 사용한 디버깅이 어렵다.



**종류**

- **Binary semaphore**
  - 1과 0으로 단순히 있다 없다만 표현. 가용한 자원의 개수가 1개.
- **Counting semaphore(general semaphore)**
  - 공유되는(가용한) 자원의 개수에 맞게 상태를 표현할 수 있다.



**Synchronization을 위한 semaphore**

```C++
{
	P(S)
	// Critical section
	V(S)
}
```



## Producer / Consumer

**producer**: 데이터의 생성만 담당

**consumer**: 데이터의 사용만 담당

producer가 버퍼에 데이터를 쓰기만 하고 consumer는 버퍼에서 데이터를 읽기만 하는 모델에서 producer가 쓰기 연산을 너무 빨리 수행한다면 버퍼가 가득 차게되므로 대기해야 하고, consumer가 읽기 연산을 너무 빨리 수행한다면 버퍼가 비게되므로 대기해야 한다.

**공유 자원**

- data와 buf

**구현**

```c++
init() {
    buf_avail = 1;
    data_avail = 0;
}

producer() {
	P(buf_avail);
	buf = data;
	V(data_avail);
}

consumer() {
    P(data_avail);
    data = buf;
    V(buf_avail);
}
```



**issue**

세마포어는 Unstructed Programming Construct 로 P와 V중 하나가 빠지거나, 인자를 검사하지 않는다. 따라서 세마포어를 Synchronization 목적으로 사용했는지, Scheduling 목적으로 사용했는지 명확히 알 수 없다.



**Solution**

세마포어가 mutual exclusion을 목적으로 할 경우 mutex_lock, mutex_unlock으로 네이밍. mutex_unlock 은 대부분의 기능이 unlock 과 동일하며 mutex_lock을 호출한 주체가 자신인지 확인하는 기능이 추가됨.



## Semaphore Implementation

실제 세마포어는 정수형 변수와 대기중인 프로세스를 관리하는 큐로 구성.

```c++
struct semaphore {
	int count;
	queue queue;
}
void wait(semaphore s) {
    disableInterrupt();
	if(s.count-- > 0) // 자원을 사용할 수 있다.
    	enableInterrupt();
	else {
		sleep(s.queue); // 자원을 사용할 수 있을 때 까지 대기.
	}
}
void signal(semaphore s) {
    disableInterrupt();
	if(s.count++ >= 0) // 자원을 대기하는 프로세스가 없다.
    	enableInterrupt();
	else {
        wakeup(s.queue); // 자원을 대기하는 프로세스를 깨운다.
    }
}
void sleep(queue queue) {
    // cur_p means current process
    enqueue(cup_p, Q);
    enableInterrupt();
    yield_cpu();
}
void wakeup(queue queue) {
    p = dequeue(queue);
    enableInterrupt();
    // execute p
    reschedule(p);
}
```

세마포어 자체도 동시 접근을 막기위해 인터럽트 제한으로 동기화. 이 경우 아주 짧은 시간동안 중지되므로 문제가 되지 않는다.

세마포어 count가 음수인 경우엔 절대값이 큐에 대기중인 프로세스 수를 나타낸다. 양수인 경우는 가용한 자원의 수를 나타낸다.

현재의 구현방법은 어디까지나 단일 프로세서 환경에서 적용된다. 한 CPU의 인터럽트를 제한 하는것 만으로는 다른 CPU가 접근함을 막을 수 없다.

한 CPU가 자원에 접근하는 동안엔 다른 CPU의 접근을 막는 atomic operation이 필요하다.



**Solution = Bus의 장악**

atomic operation 수행 동안 bus를 장악하여 다른 processor의 bus transition을 차단. 대표적으로 TAS(test and set) operation



**TAS**

메모리의 boolean 값을 읽어서 확인한 후 값을 1로 쓰는 작업을 atomic하게 수행하는 명령어.

TAS는 멀티 프로세서 환경에서 동기화를 제공하지만 busy waiting의 문제가 발생.



**busy waiting (spinning)**

루프를 돌며 특정 조건의 만족 여부를 확인하는 기법. 크게 의미가 없는 작업을 반복하며 자원을 불필요하게 소모.