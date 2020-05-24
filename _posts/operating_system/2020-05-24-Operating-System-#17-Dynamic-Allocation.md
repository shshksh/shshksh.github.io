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
  - 제약적인 구조. 연산은 항상 스택의 탑에서만 일어나기 때문에 제약이 강하다.
- Heap allocation
  - 메모리의 할당과 해제에 대한 2가지 기능을 가진다.