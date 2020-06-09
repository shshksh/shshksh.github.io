페이징의 문제점

하나의 프로세스를 여러개의 특성이 다른 유닛으로 구성되어 있으니 세그먼트로 분리하자. 메모리 억세스, 쉐어링 용이

하지만 페이징은 세그먼트 분리를 하지 않아서 과거와 같은 문제가 발생

paged segmentation

segmentation 과 paging 각각의 장점을 한꺼번에 얻기 위해 두 기법을 도시에 사용. 이를 위해 먼저 segmentation 을 수행하고 각 segment 별로 paging 을 수행

이슈

paging table , segment table 의 구성

- 기존 segment table 의 base address 대신 해당 segment 의 page table 위치를 저장
- 기존 bound 값 대신 해당 segment 의 page 개수를 저장



단점

메모리 접근 시 추가적으로 2번의 접근이 더 발생한다. 성능의 하락.



메모리 주소를 변환하는것은 MMU 에 의해 HW 적으로 이루어진다. 하지만 프로세스의 문맥 전환이나 새로운 프로세스의 생성등이 일어날 때, 운영체제가 Base/Bound Register 및 segment/page table 들을 관리해야 하므로 운영체제에게 Trasparent 하지는 않는다.



주소 변환 시 고려할 점

- space overhead: pure paging 에 비해 space overhead 를 줄일 수 있다. 
- performance overhead: extra memory access 가 2회 추가됨.



page frame 의 속성

- mapped page: 전통적인 메모리 접근 방법.
- unmapped page: physical memory 를 사용하여 별도의 memory address translation 없이 바로 접근 가능한 page. page table 등에 사용됨.
- cached page: 한번 특정 word 가 억세스 되면 빈 공간에 저장 하고 나중에 hit.
- uncached page: 캐싱을 하지 않음. DMA 의 대상 page 처럼 여러 프로세스에 의해 동시에 읽고 쓰기가 일어날 수 있는 경우(sharing) 사용.



large page table 의 문제 해결 조건

- page table 의 크기를 줄임.
- 연속적인 메모리 공간 요구량을 줄임.



 TLB(Tlanslate Lookaside Buffer)

- 테이블에 접근하기 위한 추가적인 메모리 참조는 프로그램의 성능을 두 배 내지 세 배 가까지 하락시킨다.

- 논리 주소에서 물리 주소로 변환하기 위해서는 페이지 테이블 룩업 과정이 필수적인데, 이 중 자주 룩업되는 페이지 엔트리를 캐시하면 성능을 끌어올릴 수 있다.

- 이슈: 히트 레이트

  - TLB 의 사이즈를 키운다 -> 비용 상승. 하지만 64 ~ 128 개의 TLB 만으로도 98% 가량의 높은 적중률이 나타난다. why? **locality**
  - spatial locality
  - temporal locality

  