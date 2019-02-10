# CHAPTER 6. Deployment

## 환경
- 소프트웨어가 CD 파이프라인 단계를 거치면서 다양한 종류의 환경에 배포된다.
- 배포하고자 하는 서비스는 모든 다양한 환경에서 동일해야 한다. 그러나 각각의 환경은 각기 다른 목적이 있다. 
- 랩톱에서 빌드 서버로 다시 UAT를 거쳐 실환경까지 이동하는 과정에서 점점 더 실환경에 가까워져서 이런 환경적인 차이점과 관련된 문제를 조만간 찾을 수 있게 되기를 원할 것이다. 이는 지속적인 균형을 요구한다.
- 유사 실환경과 빠른 피드백 사이의 균형은 고정된 것이 아니며 하위 시스템과 피드백에서 발견되 버그를 지켜보고 필요에 따라 적절히 균형을 조절해야한다.

## 서비스 환경 구성
- 환경마다 바뀌어야 하는 환경 구성은 전적으로 최소화되어야하낟. 
- 한 가지 방법은 환경당 하나의 산출물을 빌드하고 환경 구성을 그 산출물에 포함시키는 것이다. 이는 처음에는 합리적으로 보이나 검증을 확신할 수 없다.
- 더 나은 방법은 단일 산출물을 생성하여 환경 구성을 분리해서 관리하는 것이다. 이것은 각 환경에 존재하는 속성 파일 또는 설치 프로세스에 전달될 다른 매개 변수와 같은 것이 될 수 있다. 
- 대규모 마이크로서비스를 다룰 때 이용되는 방법은 환경 구성을 제공하는 전용 시스템을 이용하는 것이다.

## 서비스와 호스트 매핑
> 다양한 배포 모델을 생각할 때 호스트에 대해 이야기하고 호스트당 얼마나 많은 서비스를 넣을 것인지 대해 논의할 것이다.

### 호스트당 다수의 서비스
- 장점
  - 호스트 관리 관점에서 단순하다.
  - 비용 :  가상 호스트를 프로비저닝하고 크기를 조절할 수 있는 가상화 플랫폼을 접하더라도 가상화 자체의 부하로 인해 서비스가 사용할 하부 리소스를 축소시킨다.
- 단점
  - 모니터링이 어렵다. 
  - 한 서비스에 몰린 불규칙한 부하가 그 호스트에서 실행되는 모든 서비스에 부정적인 영향을 줄 수 있다.
  - 다른 곳에 문제를 일으키지 않는 배포를 보장해야 하므로 서비스를 배포하는 일 역시 좀 더 복잡하다.
  - 배포 산출물 방식을 제한한다. 
  - 서비스를 최대로 확장하기 위해 복잡한 노력이 필요하다. 서비스의 요구사항이 다르더라도 모두 같은 식으로 처리해야한다.

### 애플리케이션 컨테이너
- 장점
  - 인스턴스들의 그룹화나 모니터링 도구 등을 다루는 클러스터링 지원처럼 관리성이 향상된 측면에서 많은 혜택을 가져다준다.
  - 언어 런터임(여기서는 JVM)의 부하를 줄인다.
  - 더 이상 감당할 수 없는 자원 부족을 최적화하기 위한 시도이다.
- 단점
  - 필연적으로 기술 선택을 제약한다. 서비스를 구현하는 기술의 선택 범위뿐만 아니라 시스템의 자동화와 관리 관점에서 취할 수 있는 방안도 제한할 것이다.
  - 컨테이너 대부분은 공유 인메모리 세션 상태를 지원하는 클러스터를 관리할 능력을 과장하고 있다. 
  - 통합된 모니터링을 고려한다면 컨테이너가 제공하는 모니터링 기능만으로 충분하지 않다. 그 기능 대부분은 기동 시간이 느려서 개발자 피드백 사이클에 영향을 준다.
  - 대부분 상용이라 비용 문제 외에도 그 자체로 자원에 대한 추가 부담이 있다.

### 호스트당 단일 서비스
- 장점
  - 쉬운 모니터링과 복원 기능을 제공하면서 호스트당 다수 서비스 모델의 부작용을 회피하고 잠재적으로 단일 장애 지점을 줄인다.
  - 한 소스트의 고장은 오직 하나의 서비스에만 영향을 미친다.
  - 특정 서비스를 다른 서비스와 독립적으로 확장할 수 있다.
  - 보안 문제와 관련해서 해당 서비스와 호스트만 집중할 수 있으므로 더 쉽게 처리가 가능하다.
- 단점
  - 증가된 호스트는 관리해야 할 더 많은 서버가 생성되었음을 의미한다.
  - 개별 실행되는 더 많은 호스트로 인한 비용이 증가된다.

### 서비스로서의 플랫폼 (Paas : Platform as a Service)
- 장점
  - 단일 호스트보다 더 높은 수준의 추상화 대상에서 작업한다.
  - 대신해서 투명하게 시스템을 확장하고 축소한다.
- 단점
  - 제대로 작동하지 않을 경우에는 문제를 고치기 위해 내부를 살펴보려는 측면에서 통제할 수 있는 게 그다지 많지 않다.
  - 비표준적인 애플리케이션이 많아질수록 제대로 동작하지 않을 가능성 또한 높다.

## 자동화
- 호스트당 단일 서비스 구축시 모든 관리를 수작업으로 한다면 서버가 두 배가 되면 일도 두배가 된다. 그러나 호스트의 통제와 서비스의 배포를 자동화한다면 호스트 수의 증가가 작업량을 선형적으로 증가시키는 이유가 되지 못한다.
- 자동화는 개발자가 생산성을 유지하게 만들 수 있는 방법이다. 개별 서비스 또는 서비스 그룹을 셀프 프로비저닝할 수 있는 능력을 주는 것이 좋다. 이상적으로 조기에 문제를 발견할 수 있도록 개발자가 실환경에 서비스를 배포하는 데 사용한 것과 똑같은 도구 체인을 사용해야한다.

## 물리 머신에서 가상화로
> 많은 수의 호스트를 관리하는 데 사용할 수 있는 핵심적 수단 중 하나는 기존의 물리 머신을 더 작은 부분으로 묶는 방법을 찾는 것이다.
- 전통적 가상화 
  - 타입 2 가상화 (type 2 virtualization)로 불리며, AWS, VMWare, VSpere, Xen, KVM에 의해 구현된 것과 같은 것이다. (타입 1 가상화는 VM들이 다른 운영 체제가 아닌 하드웨어 상에서 직접 실행되는 기술을 나타낸다.) 물리적 인프라스트럭처 위에는 호스트 운영체제가 있고 이 운영 체제 위에 하이퍼파이저로 불리는 것을 실행한다. 이것은 두 가지 주요한 역할을 하는데, 첫째는 CPU와 메모리 같은 자원을 가상 호스트로부터 물리적인 호스트로 매핑하고 둘째는 우리가 가장 머신을 조작하도록 통제 계층과 같은 역할을 한다.
- 문제는 하이퍼바이저가 자신의 작업을 위해 자원을 확보해야하는 것이다. 결국 다른곳에서 사용딜 수 있는 CPU, I/O, 메모리를 소진해버리는 것이다. 하이퍼바이저는 관리할 호스트가 더 많을수록 더 많은 자원이 필요하게 되고, 결국 특정 시점에 이 부담은 물리적인 인프라스트럭처를 더 이상 분리할 수 없게 되는 한계에 도달한다. 하이퍼바이저의 오버헤드로 더 많은 리소스가 비례해서 사용된다.
- 베이그런트
  - 일반적으로 실환경보다는 테스트용으로 사용된다.
  - 랩톱에 가상의 클라우드를 제공하며, 내부적으로 표준 가상화 시스템을 사용한다.
  - 로컬 머신 상에 실환경과 유사한 환경을 쉽게 생성하도록 도와준다. 다수의 VM을 한번에 쉽게 가동할 수 있고, 장애모드를 테스트하기 위해 개별적으로 정지시킬 수 있다.
  - VM을 로컬 디렉터리에 매핑해서 변경하고 반영된 것을 즉시 확인할 수 있다.
  - 단점 중 하나는 평범한 개발용 머신에 많은 VM을 실행하는 것이 부담이 된다 작업을 관리할 수 있도록 일부 의존성을 스텁화해야 할 필요가 있다.
- 리눅스 컨테이너 
  - 분리된 가상의 호스트를 구분하고 통제하기 위해 하이퍼바이저 대신 다른 프로세스들을 위해 분리된 프로세스 공간을 생성하는 것이다.
  - 리눅스 상에서 프로세스는 특정 사용자에 의해 실행되고 설정된 퍼미션을 기반으로 특정 기능을 사용할 수 있다. 프로세스는 다른 프로세스를 생성할 수 있다. 리눅스 커널의 역할은 이러한 프로세스 트리를 관리하는 것이다.
  - 하이퍼바이저가 필요없고, 비록 컨테이너가 각자의 운영 체제를 실행하더라도 동일한 커널을 공유해야한다(커널은 프로세스 트리가 존재하는 곳이기 때문이다). 이는 호스트 운영체제를 컨테이너가 커널을 공유하는 한 호스트 운영 체제가 우분투를 실행시키고, 컨테이너가 CentOS를 실행시킬 수 있음을 의미한다.
  - 무거운 가상화 머신보다 프로비저닝이 훨씬 빠르다. 
  - 컨테이너의 경량성 덕분에 동일한 하드웨어에서 VM보다 더 많은 컨테이너를 실행시킬 수 있다. 컨테이너당 하나의 서비스를 배포함으로써 (완벽하지 않더라도) 다른 컨테이너와 일정 수준의 격리를 할 수 있다. VM당 하나의 서비스를 가질 때보다 훨씬 더 비용 효율이 좋을 것이다.
  - 무거운 가상화와도 잘 어울릴 수 있는데 다수의 프로젝트에서 큰 AWS EC2 인스턴스를 프로비전하고 그 안에 LXC 컨테이너들을 실행시켜 두 세계의 장점을 취하는 것을 목격했는데, 이는 매우 유연한 EC2 형태로 대표되는 주문형 컴퓨팅 플랫폼과 그 위에서 빠르게 실행되는 컨테이너의 조합이다.
  - 문제는 컨테이너를 직접 노출하기 위해 과도한 시간을 IPTable** 을 이용한 포트 포워딩을 설정하는데 사용할 수 있다.
  - 컨테이너들이 서로 완전히 고립될 수는 없다.
  - 한 컨테이너의 프로세스가 다른 컨테이너 또는 하부의 호스트를 망가뜨리고 상호작용하는 많은 방법이 문서화되었고 알려져있다.
- 도커
  - 경량 컨테이너 상에 구축된 플랫폼이다.
  - VM세계에서의 이미지와 동일한 의미인 애플리케이션을 생성하고 배포할 수 있다.
  - 컨테이너 프로비저닝으 관리하고, 네트워킹 문제를 처리하며 도커 애플리케이션을 저장하고 버전을 관리할 수 있도록 자체의 레지스트리 개념까지도 제공한다.
  - 도커의 애플리케이션 추상화는 VM이미지처럼 서비스를 구현하는 데 사용된 하부의 기술을 은폐하므로 유용하다. 서비스를 위해 도커 애플리케이션을 생성하도록 빌드하고 도커 레지스트리에 저장하면 끝이다. 
  - 일부 단점을 안화하기 위해 도커 플랫폼 자체를 셋업하고 해체하기 위해 베이그런트를 사용하고, 개별 서비스의 빠른 프로비저닝을 위해 도커를 사용한다. 
  - 도커를 단일 머신에서 작동하는 단순한 PaaS라고 생각해야하며 많은 머신과 도커의 경계를 넘어 서비스들을 관리하기 위한 도구를 원한다면 이러한 능력을 탑재한 다른 소프트웨어를 살펴봐야할 것이다.

## 배포 인터페이스  
- 사용하는 하부 플랫폼과 산출물이 어떤 것이든 간에 특정한 서비스를 배포하는 데 일관된 인터페이스를 유지해야한다.
