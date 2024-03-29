---
title: Kubernetes 관련자료
date: 2023-05-10 15:21:00 +0900
categories: [xt, Dev, Kubernetes]
tags: [xt, Kubernetes]
---

# Index
- 쿠버네티스란?
- 왜 쓸까?
- 마스터 노드와 워커 노드
- 컨테이너
- 팟
- 노드
- 레플리카셋
- 디플로이먼트
- 네트워크
- 컨트롤러


## 쿠버네티스란?

* 쿠버네티스는 컨테이너 오케스트레이션 도구의 일종
  * 컨테이너 오케스트레이션이란 시스템 전체를 통괄, 여러 개의 컨테이너를 관리하는 일을 맡았다. 
* 오케스트레이션
  * ![kubernetes-5.png](/assets/img/posts/kubernetes/kubernetes-5.png)

## 왜 사용하나?

* 전통적인 환경에서의 배포 > 가상환경에서의 배포 > 다시 컨테이너로의 배포 과정을 보면서 이해할 수 있다.
  * ![kubernetes-1.png](/assets/img/posts/kubernetes/kubernetes-1.png)
* 전통적인 환경에서의 배포
  * 초기 개발은 물리적 서버에서 애플리케이션 실행
  * ![kubernetes-2.png](/assets/img/posts/kubernetes/kubernetes-2.png)
  * 한 물리 서버에서 여러 애플리케이션의 리소스 한계를 정의할 방법이 없었다.
  * 예를 들어 물리 서버 하나에서 여러 애플리케이션을 실행하면, 리소스 전부를 차지하는 애플리케이션 인스턴스가 있을 수 있고, 결과적으로는 다른 애플리케이션의 성능이 저하될 수 있었다. 이에 대한 해결책은 서로 다른 여러 물리 서버에서 각 애플리케이션을 실행하는 것이 있다. 그러나 이는 리소스가 충분히 활용되지 않는다는 점에서 확장 가능하지 않았으므로, 물리 서버를 많이 유지하기 위해서 조직에게 많은 비용이 들었다.
  
* 가상 환경에서의 배포
  * 그 해결책으로 가상화가 도입되었다. 이는 단일 물리 서버의 CPU에서 여러 가상 시스템 (VM)을 실행할 수 있게 한다. 가상화를 사용하면 VM간에 애플리케이션을 격리하고 애플리케이션의 정보를 다른 애플리케이션에서 자유롭게 액세스 할 수 없으므로, 일정 수준의 보안성을 제공할 수 있다.
  * 가상화를 사용하면 물리 서버에서 리소스를 보다 효율적으로 활용할 수 있으며, 쉽게 애플리케이션을 추가하거나 업데이트할 수 있고 하드웨어 비용을 절감할 수 있어 더 나은 확장성을 제공한다. 가상화를 통해 일련의 물리 리소스를 폐기 가능한(disposable) 가상 머신으로 구성된 클러스터로 만들 수 있다.
  * 각 VM은 가상화된 하드웨어 상에서 자체 운영체제를 포함한 모든 구성 요소를 실행하는 하나의 완전한 머신이다.
  * ![kubernetes-3.png](/assets/img/posts/kubernetes/kubernetes-3.png)
  
* 컨테이너로의 배포
  * 가상머신과 유사하지만, 자체 파일 시스템, CPU 공유, 메모리, 프로세스 공간 등이 있으며 애플리케이션간에 OS를 공유한다. 기본 인프라에서 분리되어서 클라우드 및 OS 전반에 걸쳐 이식 가능
    * ![kubernetes-4.png](/assets/img/posts/kubernetes/kubernetes-4.png)
  * VM 이미지를 사용하는 것에 비해 컨테이너 이미지 생성이 보다 쉽고 효율적임.
  * 안정적이고 주기적으로 컨테이너 이미지를 빌드해서 배포할 수 있고 (이미지의 불변성 덕에) 빠르고 효율적으로 롤백할 수 있다.
  * 배포 시점이 아닌 빌드/릴리스 시점에 애플리케이션 컨테이너 이미지를 만들기 때문에, 애플리케이션이 인프라스트럭처에서 분리된다.
  * OS 수준의 정보와 메트릭에 머무르지 않고, 애플리케이션의 헬스와 그 밖의 시그널을 볼 수 있다.
  * 개발, 테스팅 및 운영 환경에 걸친 일관성: 랩탑에서도 클라우드에서와 동일하게 구동된다.
  * 클라우드 및 OS 배포판 간 이식성: Ubuntu, RHEL, CoreOS, 온-프레미스, 주요 퍼블릭 클라우드와 어디에서든 구동된다.
  * 느슨하게 커플되고, 분산되고, 유연하며, 자유로운 마이크로서비스: 애플리케이션은 단일 목적의 머신에서 모놀리식 스택으로 구동되지 않고 보다 작고 독립적인 단위로 쪼개져서 동적으로 배포되고 관리될 수 있다.
  * 리소스 격리: 애플리케이션 성능을 예측할 수 있다.
  * 리소스 사용량: 고효율 고집적.

* 프로덕션 환경에서는 애플리케이션을 실행하는 컨테이너를 관리하고 가동 중지 시간이 없는지 확인해야 한다. 예를 들어 컨테이너가 다운되면 다른 컨테이너를 다시 시작해야 한다. 이 문제를 시스템에 의해 처리한다면 더 수월하다.
* 실제 운영 환경에서 여러 애플리케이션들은 여러 컨테이너에 걸쳐 있으며 이러한 컨테이너는 여러 서버 호스트에 배포되어야한다. 따라서 컨테이너를 위한 보안은 멀티 레이어 구조로 되어 있고 복잡할 수 있는데, 쿠버네티스를 활용하면 이러한 워크로드를 위해 규모에 맞는 컨테이너를 배포하는데 필요한 오케스트레이션 및 관리 기능을 제공한다.
* IT 서비스의 사용량이 항상 같지 않기 때문에 서버 자원을 효율적으로 활용하기 위해 kubernenes의 가상화 서비스를 사용한다.

## 마스터 노드와 워커 노드

* 마스터 노드는 전체적인 제어를, 워커 노드는 실제 동작을 담당한다. 
* 마스터 노드는 워커 노드에서 실행되는 컨테이너를 관리하는 역할
* 워커 노드는 실제 서버에 해당, 실제 컨테이너가 동작하는 서버. 따라서 컨테이너 엔진이 설치돼야한다. 
* 마스터와 워커 노드로 구성된 일군의 쿠버네티스 시스템을 클러스터라고 한다.
* 마스터 노드는 컨트롤 플레인을 통해 워커 노드를 관리
* 워커노드란 실제로 애플리케이션이 실행되고 있는 곳이라고 할 수 있다. 


## 컨테이너

* 쿠버네티스는 컨테이너를 관리하고 오케스트레이션하기 위한 기술
  * 컨테이너 이미지뿐만 아니라 실행 항목과 실행 방법에 대한 정보를 포함하는 개념
  * 컨테이너는 자체 네트워크 및 접근 권한도 갖는다. 이 네트워크는 컨테이너가 실행되고 있는 리눅스와 공유하고 있다. 
  * 싱글 프로세스로 동작

## 팟

* 쿠버네티스가 관리하는 가장 작은 단위, 시스템을 구축하는 기본 단위
* 1개 이상의 컨테이너와 관련된 정보들로 구성
* 팟을 구성하는 컨테이너 목록과 해당 팟이 다른 팟과 상호작용하기 위해 필요한 메타데이터, SW 실행 실패 시 정책 등이 포함된 데이터 구조를 반환
* 쉽게 생성되고 삭제할 수 있어 수명이 매우 짧다. 
* 쿠버네티스에서의 팟은 다음과 같은 사항을 보장한다
  * 팟의 모든 컨테이너는 동일한 노드에서 실행된다.
  * 동일한 팟에 속한 컨테이너들은 동일한 네트워크에 포함되며 상호 통신이 가능하다.
  * 동일한 팟에 속한 컨테이너들은 팟에 연결된 볼륨을 통해 파일 공유가 가능하다.
  * 팟은 수명 주기를 갖고 있으며, 다시 생성될 경우 반드시 처음 실행됐던 노드에서 실행된다. 
* 쿠버네티스는 팟의 상태뿐 아니라 팟을 구성하는 컨테이너의 상태까지 관리하고 리포팅하는 기능을 제공
* 컨테이너의 상태는 Running, Terminated, Waiting 등으로 표시
* 팟은 Phase와 PodStatus로 구성
  * Phase는 Pending, Running, Succeed, Fail, Unknown 중 하나로 표시
* 팟은 컨테이너의 상태 정보를 체크할 수 있는 Probe를 포함할 수 있다.
  * 일반적으로 livenessProbe와 readnessProbe 두 종류의 Probe가 쿠버네티스 컨트롤러에 의해 배포되고 사용된다. 
  * liveness는 컨테이너가 실행 중인지 여부를 주기적으로 체크
  * readinessProbe는 컨테이너가 서비스 가능한 상태인지 주기적으로 체크
  * 일반적으로 Probe는 컨테이너 내부에 있는 SW로부터 상태 정보를 체크할 수 있도록 설정



## 노드

* 노드는 일반적으로 쿠버네티스 클러스터에 추가된 리눅스가 설치된 머신을 의미
* 쿠버네티스 클러스터는 여러 개의 노드로 구성
* 노드의 형태는 물리 머신이나 가상 머신 모두 가능
* 쿠버네티스는 팟을 서로 연결하거나 외부에서 접근 가능하도록 하는 역할을 수행할 뿐 아니라 리소스 사용량을 추적해 클러스터를 구성하는 노드 전체에 팟을 스케줄링하고 실행함으로써 리소스를 관리한다.



## 레플리카셋

* 팟의 상위 개념, 동시에 실행해야하는 팟의 수를 정의, 디플로이먼트의 하위 개념
* 동시에 생성해야 할 팟의 수를 정의해 시스템의 수평 확장을 구현하는 데 매우 중요한 역할




## 디플로이먼트

* 쿠버네티스에서 코드를 실행할 때 가장 보편적이고 권장되는 방법은 디플로이먼트 컨트롤러가 관리하는 디플로이먼트 리소스를 통해 코드를 배포하는 것이다.
* 디플로이먼트 컨트롤러는 레플리카셋 컨트롤러의 상위 개념, 레플리카셋 컨트롤러의 확장된 개념이다. 
* SW에 대한 롤링 업데이트 기능을 지원, 리소스를 새로운 버전의 SW와 함께 배포할 때 롤 아웃 프로세스를 관리
* 항상 실행 상태를 유지해야하는 팟의 수가 정의된 메타 데이터를 포함, 따라서 업데이트 요청 시 새로운 버전의 컨테이너를 추가하고 오래된 버전의 컨테이너 삭제를 통해 시스템 중단이 없는 SW 롤링 업데이트가 가능하다.


## 네트워크

* 동일한 팟에 위치한 모든 컨테이너는 노드의 네트워크를 공유한다
* 쿠버네티스 클러스터의 모든 노드는 서로 연결돼 있으며 프라이빗 네트워크를 공유한다. 
* 쿠버네티스가 팟 내에서 컨테이너를 실행할 경우 컨테이너는 격리된 네트워크에서 실행
* 쿠버네티스는 IP를 관리, DNS를 매핑해 쿠버네티스 클러스터 내의 팟들이 서로 통신이 가능하도록 한다. 


## 컨트롤러

* 리소스를 추적하고 사용자가 요청한 내용을 해석해 SW를 실행하는 두뇌와 같은 역할
* 쿠버네티스는 팟 내부에서 실행되고 있는 SW 버전을 업데이트하고, 쿠버네티스 클러스터를 구성하는 노드 장애를 감지하고 처리한다. 




## 참고
[쿠버네티스 팟 공식문서](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-phase)
[쿠버네티스 개념과 구성요소](https://www.codestates.com/blog/content/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4)
[쿠버네티스란 무엇인가?](https://kubernetes.io/ko/docs/concepts/overview/)



