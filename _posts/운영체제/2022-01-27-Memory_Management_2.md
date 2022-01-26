# 3. Allocation of Physical Memory

### Noncontiguous allocation

- 주소 변환이 복잡해짐



**Paging**

- Process의 virtual memory를 동일한 사이즈의 page 단위로 나눔
- virtual memory의 내용이 page 단위로 불연속적으로 저장됨
- 일부는 backing storage에, 일부는 physical memory에 저장

![paging](/assets/post-img/os/paging.png)

- Basic Method
  - physical memory를 동일한 크기의 frame으로 나눔
  - logical memory를 동일한 크기의 page로 나눔 (frame과 같은 크기)
  - 모든 가용 frame들을 관리
  - page table을 사용하여 logical address를 physical address로 변환
    - page table: 각각의 논리적인 page들이 물리적인 page frame 어디에 올라가 있는지에 대한 정보를 저장
- External fragmentation 발생하지 않지만, Internal fragmentation은 발생 가능
  - 프로그램의 크기가 반드지 페이지 크기의 배수가 될 것이라는 보장은 없음
  - 프로세스를 페이지 단위로 나누다 보니 가장 마지막 페이지에서는 페이지 하나보다 남은 용량이 더 작을 수 있기 때문에 Internal fragmentation이 발생할 수 있음

- Implementation of Page Table

  ![TLB](/assets/post-img/os/TLB.png)

  - Page table은 main memory에 상주
  - **page-table base register (PTBR)**가 page table을 가리킴
  - **page-table length register (PTLR)**가 테이블 크기를 보관
  - 모든 메모리 접근 연산에는 **2번의 memory access**가 필요
    - page table 접근 1번, 실제 data/instruction 접근 1번
  - 속도 향상을 위해 assiciative register 혹은 translation look-aside buffer(**TLB**)라 불리는 고속의 lookup hardware cache 사용
    - TLB는 주소 변환을 위한 cache 메모리. 데이터를 저장하는 cache 메모리와는 다름
    - 메모리에 접근하기 전 TLB를 먼저 확인해 TLB에 저장되어 있는 정보를 사용해 주소 변환이 가능한지 확인. 있다면 메모리를 한 번 접근, 없다면 메모리에 2번 접근하게 됨
    - TLB는 전체를 search해야 함. 따라서 시간을 줄이기 위해 associative register를 통해 parell search 가 가능
    - TLB는 context switch 때 flush (remove old entries)됨