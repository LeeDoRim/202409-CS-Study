# Chapter 03. 운영체제


# 1. 운영체제의 큰 그림

- 운영 체제의 핵심 기능 2가지
    1. 자원 할당 및 관리
    2. 프로세스 및 스레드 관리
- 프로세스 및 스레드
    - 프로세스 : 실행 중인 프로그램
    - 스레드 : 프록세스를 이루는 실행의 단위
- 시스템 콜 : 운영체제의 서비스를 제공받기 위한 수단으로, 호출 가능한 함수의 형태를 가진다.

# 2. 프로세스와 스레드

- 프로세스 메모리
    - 커널 영역에는 프로세스 제어 블록(PCB) 정보가 저장되고, 사용자 영역에는 실행 중인 프로세스가 코드 영역, 데이터 영역, 힙 영역, 스택 영역으로 나뉘어 저장된다.
    - 코드 영역 : CPU가 읽고 실행할 명령어가 담겨 있어 읽기 전용 공간이다.
    - 데이터 영역 : 프로그램이 실행되는 동안 유지할 데이터가 저장되는 공간이다.
    - 힙 영역 : 프로그램을 만드는 개발자가 직접 할당 가능한 저장 공간이다.
    - 스택 영역 : 데이터 영역에 담기는 값과는 달리 일시적으로 사용할 값들이 저장되는 공간이다.
- PCB 정보는 대표적으로 프로세스ID, 실행 과정에서 사용한 레지스터 값, 프로세스 상태, CPU 스케줄링 정보, 메모리 관련 정보, 파일 및 입출력장치 관련 정보가 명시된다.
- 문맥 : 프로세스가 수행을 재개하기 위해 기억해야할 정보
- 문맥 교환 : 기존 프로세스의 문맥을 PCB에 백업하고, PCB에서 문맥을 복구하여 새로운 프로세스를 실행하는 것이다.

- 프로세스의 상태
    - 생성 상태(new) : 프로세스 생성 중인 상태
    - 준비 상태(ready) : 당장이라도 CPU를 할당받아 실행할 수 있지만, 아직 자신의 차례가 아니기 때문에 기다리고 있는 상태
    - 실행 상태(running) : CPU를 할당받아 실행 중인 상태
    - 대기 상태(blocked) : 프로세스가 입출력 작업을 요청하거나 바로 확보할 수 없는 자원을 요청하는 등 곧장 실행이 불가능한 조건에 놓이는 경우이다.
    - 종료 상태(terminated) : 프로세스가 종료된 상태이다.\

- 멀티 프로세스와 멀티 스레드의 가장 큰 차이점은 자원의 공유 여부이다.
    - 서로 다른 프로세스들은 기본적으로 자원을 공유하지 않기 때문에 독립적으로 실행된느 반면, 같은 프로세스를 실행하는 여러 스레드들은 프로세스의 자원을 공유한다.
    - 스레드들은 동일한 주소 공간의 코드, 데이터, 힙 영역을 공유하고, 열린 파일과 같은 프로세스의 자원을 공유하기 때문에 쉽게 협력하고 통신할 수 있다. 다만, 멀티 프로세스 환경에서는 한 프로세스에 문제가 생겨도 다른 프로세스에는 지장이 없거나 적지만, 멀티스레드 환경에서는 한 스레드에 생긴 문제가 프로세스 전체의 문제가 될 수 있다.
    
- 프로세스 간 통신(IPC) 방법
    1. 공유 메모리
    2. 메시지 전달

# 3. 동기화와 교착상태

- 임계 구역 : 공유 자원에 접근하는 코드 중 동시에 실행했을 때 문제가 발생할 수 있는 코드
- 레이스 컨디션 : 프로세스 혹은 스레드가 동시에 임계 구역의 코드를 실행하여 문제가 발생하는 상황이다. 레이스 컨디션을 방지히면서 임계 구역을 관리하기 위해서는 프로세스와 스레드가 동기화되어야 한다.
- 프로세스 혹은 스레드의 동기화 조건
    1. 실행 순서 제어 : 프로세스 및 스레드를 올바른 순서로 실행하기
    2. 상호 배제 : 동시에 접근해서는 안 되는 자원에 하나의 프로세스 및 스레드만 접근하기
- 동기화 기법
    1. 뮤텍스 락 : 동시에 접근해서는 안되는 자원에 동시에 접근 불가능 하도록 상호 배제를 보장하는 동기화 도구이다.
        - 뮤텍스 락의 원리 : 임계 구역에 접근하고자 한다면 반드시 락(lock)을 획득(acquire)해야 하고, 임계 구역에서의 작업이 끝났다면 락을 해제(release)해야 한다.
    2. 세마포 : 공유 자원이 여러 개 있는 상황에서도 동기화가 가능하다. 프로세스 및 스레드가 임계 구역 앞에서 멈추라는 신호를 받으면 잠시 기다리고, 가도 좋다는 신호를 받으면 그제서야 임계 구역에 들어가게 된다.
        - 세마포 구성
            - 변수 S :  사용 가능한 공유 자원의 개수를 나타내는 변수
            - wait() 함수 : 임계 구역 진입 전 호출하는 함수
            - signal() 함수 : 임계 구역 진입 후 호출하는 함수
    3. 모니터 : 공유 자원과 그 공유자원을 다루는 함수로 구성된 동기화 도구로, 상호 배제를 위한 동기화뿐만 아니라 실행 순서 제어를 위한 동기화까지 가능하다.
        - 모니터의 작동 원리 : 프로세스 및 스레드는 공유 자원에 접근하기 위해 반드시 정해진 공유 자원 연산을 통해 모니터 내로 진입해야 하고, 모니터 안에 진입하여 실행되는 프로세스 및 스레드는 항상 하나여야 한다. 모니터 내로 진입하여 실행 중인 프로세스 및 스레드가 있다면 큐에서 대기해야 한다.
        - 조건 변수 : 실행 순서 제어를 위한 동기화 도구로, 특정 조건 하에 프로세스를 실행/일시 중단함으로써 프로세스나 스레드의 실행 순서를 제어할 수 있다.
- 스레드 안전 : 멀티스레드 환경에서 어떤 변수나 함수, 객체에 동시에 접근이 이루어져도 실행에 문제가 없는 상태를 의미한다.

- 교착 상태 : 일어나지 않을 사건을 기다리며 프로세스의 진행이 멈춰 버리는 현상이다.
- 교착 상태의 발생 조건
    - 교착 상태는 상호 배제, 점유와 대기, 비선점, 원형 대기 중 하나라도 만족하지 않으면 발생하지 않고, 4개의 조건이 모두 만족할 때 교착 상태가 발생할 가능성이 생긴다.
    - 상호 배제 : 한 프로세스가 사용하는 자원을 다른 프로세스가 사용할 수 없는 상황
    - 점유와 대기 : 한 프로세스가 어떤 자원을 할당받은 상태(점유)에서 다른 자원 할당받기를 기다린다리는(대기) 상황
    - 비선점 : 해당 자원을 이용하는 프로세스의 작업이 끝나야만 비로소 자원을 이용할 수 있다.
    - 원형 대기 : 프로세스와 프로세스가 요청한 자원이 원 형태를 이루는 경우
- 교착 상태 해결 방법
    - 교착 상태 예방 : 교착 상태를 발생시키는 4가지 필요 조건 중 하나를 충족하지 못하게 하는 방법이다.
    - 교착 상태 회피 : 교착 상태가 발생하지 않을 정도로만 조심하면서 자원을 할당하는 방법이다.
    - 교착 생태 검출 후 회복 : 교착 상태의 발생을 인정하고 처리하는 사후 조치이다. 교착 상태가 검출되면 프로세스를 자원 선점을 통해 회복시키거나, 교착 상태에 놓인 프로세스를 강제 종료함으로써 회복시킬 수 있다. 자원 선점을 통한 회복이란 교착 상태가 해결될 때까지 다른 프로세스로부터 강제로 자원을 빼앗아 한 프로세스에 몰아서 할당하는 것을 의미한다.

# 4. CPU 스케줄링

- CPU 스케줄링 : 운영체제의 CPU 분배 방법
- CPU 활용률은 천제 CPU의 가동 시간 중 작업을 처리하는 시간의 비율을 의미합니다. 운영체제는 높은 CPU 활용률을 유지하기 위해 기본적으로 입출력 작업이 많은 프로세스의 우선순위를 높게 유지한다.
- 프로세스가 CPU를 이용하는 작업을 CPU 버스트라고 하고, 입출력장치를 기다리는 작업을 입출력 버스트라고 한다. 입출력 작업이 많은 프로세스를 입출력 집중 프로세스라고하고, CPU 작업이 많은 프로세스를 CPU 집중 프로세스라고 한다.
- 선점형 스케줄링은 운영체제가 프로세스로부터 CPU 자원을 강제로 빼앗아 다른 프로세스에 할당할 수 있는 스케줄링이다. 비선점형 스케줄링은 어떤 프로세스가 CPU를 사용하고 있을 때 그 프로세스가 종료되거나 스스로 대기 상태에 접어들기 전까지는 다른 프로세스가 끼어들 수 없는 스케줄링 방식이다.
- CPU 스케줄링 알고리즘
    1. 선입 선처리 스케줄링 : FCFS
    2. 최단 작업 우선 스케줄링 : 준비 큐에 삽입된 프로세스 중 CPU를 이용하는 시간의 실이가 가장 짧은 프로세스부터 먼저 실행하는 스케줄링 방식이다.
    3. 라운드 로빈 스케줄링 : 선입 선처리 스케줄링에 타임 슬라이스라는 개념이 더해진 스케줄링 방식으로, 타임 슬라이스란 프로세스가 CPU를 사용하도록 정해진 시간을 의미한다.
    4. 최소 잔여 신간 우선 스케줄링 : 최단 작업 우선 스케줄링과 라운드 로빈 스케줄링을 합친 스케줄링 방식이다.
    5. 우선순위 스케줄링 : 프로세스에 우선순위를 부여하고, 가장 높은 우선순위를 가진 프로세스부터 실행하는 스케줄링 방식이다.
    6. 다단계 큐 스케줄링 : 우선순위 스케줄링의 발전된 형태로, 우선순위별로 여러 개으이 준비 큐를 사용하는 스케줄링 방식이다.
    7. 다단계 피트백 큐 스케줄링 : 다단계 큐 스케줄링과 비슷하게 동작하지만, 프로세스들이 큐 사이를 이동할 수 있다.

# 5. 가상 메모리

- CPU와 프로세스는 메모의 하드웨어 상 실제 주소인 물리 주소가 아니라 논리 주소를 사용한다. 논리 주소는 프로세스마다 부여되는 0번지부터 시작하는 주소 체계이다.
- 메모리 관리 장치(MMU)는 CPU와 메모리 사이에 위치하며, CPU가 이애하는 논리 주소를 메모리가 이해하는 물리 주소로 변환하는 역할을 한다.
- 스와핑 : 오랫동안 사용되지 않은 프로세스를 임시로 스왑 영역라는 보조기억장치의 일부인 영역으로 쫒아내고 자리에 생긴 메모리 상의 빈 공간에 다른 프로세스를 적재하여 실행하는 메모리 관리 방식이다.
- 현재 실행되지 않은 프로세스가 메모리에서 스왑 영역으로 옮겨지는 것을 스왑 아웃, 반대로 스왑 영역에 있는 프로세스가 다시 메모리로 옮겨오는 것을 스왑 인이라고 한다.
- 연속 메모리 할당 : 프로세스에 연속적인 메모리 공간을 할당하는 방식으로, 외부 단편화 문제가 발생한다.
- 스와핑과 연속 메모리 할당은 외부 단편화와 물리 메모리보다 큰 프로세스를 실행할 수 없다는 문제가 있다.

- 가상 메모리는 실행하고자 하는 프로그램의 일부만 메모리에 적재해, 실제 메모리보다 더 큰 프로세스를 샐행할 수 있도록 만드는 메모리 관리 기법이다. 가상 메모리 관리 기법에는 페이징과 세그멘테이션이 있다.
- 페이징은 프로세스의 논리 주소 공간을 페이지라는 일정한 단위를 나누고, 물리 주소 공간을 페이지와 동일한 크기의 프레임이라는 일정한 단위로 나눈 뒤 페이지를 프레임에 할당하는 가상 메모리 관리 기법이다. 페이징을 사용하면 페이지라는 일정한 크기로 잘린 프로세스들을 메모리에 할당하느로 외부 단편화가 발생하지 않는다.
- 프로세스가 메모리에 불연속적으로 할당되므로 CPU가 프로세스를 이루는 어떤 페이지가 어떤 프레임에 적재되어 있는지 모두 알고 있기 어렵다. 따라서 프로세스의 페이지와 실제로 적재된 프레임을 짝지어주는 정보인 페이지 테이블을 활용한다.
- 페이징은 외부 단편화 문제를 해결할 수 있지만, 내부 단편화를 야기할 수 있다.
- 페이지 교체 알고리즘
    1. FIFO 페이지 교체 알고리즘
    2. 최적 페이지 교체 알고리즘 : 앞으로 가장 적게 사용할 페이지를 스왑 아웃해 가장 낮은 페이지 폴트율을 보장하는 알고리즘이다.
    3. LRU 페이지 교체 알고리즘 : 가장 적게 사용한 페이지를 교체하는 알고리즘이다.

# 6. 파일 시스템

- 파일 할당
    - 운영체제는 파일과 디렉터리를 블록 단위로 읽고 쓴다.
    - 각 블록의 일부에 다음 블록의 주소를 저장하여 각각의 블록이 다음 블록을 가리키는 형태로 할당하는 것을 블록 할당이라고 한다.
    - 색인 할당 : 파일을 이루는 모든 블록의 주소를 색인 블록이라는 특별한 블록에 모아 관리하는 방식으로 할당할 수 있다.
- 아이노드 기반 파일 시스템
    - 파일마다 각각의 아이노드를 가지고 있으며, 아이노드에는 각각의 번호가 부여되어 있다.
    - 아이노드 기반 파일 시스템의 구성은 다음과 같다. 부트블록과 여러 블록 그룹으로 구성되어 있으며, 블록 그룹은 슈퍼 블록, 그룹 식별자, 블록 비트맵, 아이노드 비트맵, 아이노드 테이블, 데이터 블록으로 구성된다.