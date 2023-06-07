---
title: Elastic Search 관련자료
date: 2023-06-01 15:21:00 +0900
categories: [xt, Dev, ElasticSearch]
tags: [xt, ElasticSearch]
---

# Index
--- 
## Elastic Search란?
## Elastic Search 아키텍쳐 / 용어 정리
## Elastic Search는 어떻게 작동하나요?
## Elastic Search를 사용하는 이유는 무엇인가요?
## Elastic Search와 RDBMS
## Elastic Search는 어디에 사용되나요?
## 역색인
## Elastic Search의 특징과 장단점
--- 

### ElasticSearch란?
  - 정의 : Elasticsearch는 Apache Lucene( 아파치 루씬 ) 기반의 Java 오픈소스 분산 검색 엔진입니다. Elastic Search를 통해 루씬 라이브러리를 단독으로 사용할 수 있게 되었으며, 방대한 양의 데이터를 신속하게, 거의 실시간( NRT, Near Real Time )으로 저장, 검색, 분석할 수 있습니다.
  - Elasticsearch는 검색을 위해 단독으로 사용되기도 하며, ELK( Elasticsearch / Logstash / Kibana )스택으로 사용되기도 합니다.
  - ELK 스택이란 다음과 같습니다.

    - Logstash
      - 다양한 소스( DB, csv파일 등 )의 로그 또는 트랜잭션 데이터를 수집, 집계, 파싱하여 Elasticsearch로 전달
    - Elasticsearch 
      - Logstash로부터 받은 데이터를 검색 및 집계를 하여 필요한 관심 있는 정보를 획득
    - Kibana
      - Elasticsearch의 빠른 검색을 통해 데이터를 시각화 및 모니터링
      ![elasticsearch-1.png](/assets/img/posts/elasticsearch/elasticsearch-1.png)
--- 
### Elasticsearch 아키텍쳐 / 용어 정리
  - Elasticsearch에서 사용하는 대부분의 개념은 RDBMS에도 존재하는 개념들입니다. 아래의 사진은 Elasticsearch Architecture이며, 앞으로 설명할 용어들의 구조입니다.

    - 클러스터( cluster )

      - 클러스터란 Elasticsearch에서 가장 큰 시스템 단위를 의미하며, 최소 하나 이상의 노드로 이루어진 노드들의 집합입니다.
    
      - 서로 다른 클러스터는 데이터의 접근, 교환을 할 수 없는 독립적인 시스템으로 유지되며,
    
      - 여러 대의 서버가 하나의 클러스터를 구성할 수 있고, 한 서버에 여러 개의 클러스터가 존재할수도 있습니다.
      
    - 노드( node )
      - Elasticsearch를 구성하는 하나의 단위 프로세스를 의미합니다.
      - 그 역할에 따라 Master-eligible, Data, Ingest, Tribe 노드로 구분할 수 있습니다.
      - 아래는 각 노드들에 대한 설명인데, 제가 Elasticsearch에 대한 깊이가 없어서 공식 문서의 설명들을 정리만 해보았습니다.
    - master-eligible node
      - 클러스터를 제어하는 마스터로 선택할 수 있는 노드를 말합니다.
      - 여기서 master 노드가 하는 역할은 다음과 같습니다.
        - 인덱스 생성, 삭제
        - 클러스터 노드들의 추적, 관리
        - 데이터 입력 시 어느 샤드에 할당할 것인지
    - Data node
      - 데이터와 관련된 CRUD 작업과 관련있는 노드입니다.
      - 이 노드는 CPU, 메모리 등 자원을 많이 소모하므로 모니터링이 필요하며, master 노드와 분리되는 것이 좋습니다.
    - Ingest node
      - 데이터를 변환하는 등 사전 처리 파이프라인을 실행하는 역할을 합니다.
    - Coordination only node
      - data node와 master-eligible node의 일을 대신하는 이 노드는 대규모 클러스터에서 큰 이점이 있습니다.
      - 즉 로드밸런서와 비슷한 역할을 한다고 보시면 됩니다.
---
--- 
### Elasticsearch는 어떻게 작동하나요?
  - 로그, 시스템 메트릭, 웹 애플리케이션 등 다양한 소스로부터 원시 데이터가 Elasticsearch로 흘러들어갑니다. 데이터 수집은 원시 데이터가 Elasticsearch에서 색인되기 전에 구문 분석, 정규화, 강화되는 프로세스입니다. Elasticsearch에서 일단 색인되면, 사용자는 이 데이터에 대해 복잡한 쿼리를 실행하고 집계를 사용해 데이터의 복잡한 요약을 검색할 수 있습니다. Kibana에서 사용자는 데이터를 강력하게 시각화하고, 대시보드를 공유하며, Elastic Stack을 관리할 수 있습니다.
---
--- 
### Elasticsearch를 사용하는 이유는 무엇인가요?
  - Elasticsearch는 Lucene을 기반으로 구축되기 때문에, 풀텍스트 검색에 뛰어납니다. 문서가 색인될 때부터 검색 가능해질 때까지의 대기 시간이 아주 짧아 실시간 검색 플랫폼이라고 봐도 무방합니다. 이 대기 시간은 보통 1초인데,  결과적으로, Elasticsearch는 보안 분석, 인프라 모니터링 같은 시간이 중요한 사용 사례에 이상적입니다.

  - Elasticsearch는 본질상 분산적입니다. Elasticsearch에 저장된 문서는 샤드라고 하는 여러 다른 컨테이너에 걸쳐 분산되며, 이 샤드는 복제되어 하드웨어 장애 시에 중복되는 데이터 사본을 제공합니다. Elasticsearch의 분산적인 특징은 수백 개(심지어 수천 개)의 서버까지 확장하고 페타바이트의 데이터를 처리할 수 있게 해줍니다.

  - Elastic Stack은 데이터 수집, 시각화, 보고를 간소화합니다. Beats와 Logstash의 통합은 Elasticsearch로 색인하기 전에 데이터를 훨씬 더 쉽게 처리할 수 있게 해줍니다. Kibana는 Elasticsearch 데이터의 실시간 시각화를 제공하며, UI를 통해 애플리케이션 성능 모니터링(APM), 로그, 인프라 메트릭 데이터에 신속하게 접근할 수 있습니다.
--- 
### Elastic Search와 RDBMS
  - Elastic에서 사용되는 데이터 구조를 RDBMS에 대응해보면 다음과 같이 맵핑됩니다.
  ![elasticsearch-2.png](/assets/img/posts/elasticsearch/elasticsearch-2.png)
  - Elastic Search는 기본적으로 http 프로토콜로 접근이 가능한 REST API를 통해 데이터 조작을 지원합니다. 이를 역시 RDBMS의 SQL과 맵핑해보면:
  ![elasticsearch-3.png](/assets/img/posts/elasticsearch/elasticsearch-3.png)
--- 
--- 
### Elasticsearch는 어디에 사용되나요?
- Elasticsearch의 속도와 확장성, 그리고 수많은 종류의 콘텐츠를 색인할 수 있는 능력은 다음과 같은 다양한 사용 사례에 이용될 수 있다는 뜻입니다.

1. 애플리케이션 검색
2. 웹사이트 검색
3. 엔터프라이즈 검색
4. 로깅과 로그 분석
5. 인프라 메트릭과 컨테이너 모니터링
6. 애플리케이션 성능 모니터링
7. 위치 기반 정보 데이터 분석 및 시각화
8. 보안 분석
9. 비즈니스 분석
---
--- 
### 역색인
   - 일반적인 색인의 목적은 ‘문서의 위치’에 대한 index를 만들어서 빠르게 그 문서에 접근하고자 하는 것인데, 역색인은 반대로 ‘문서 내의 문자와 같은 내용물’의 맵핑 정보를 색인해놓는 것이다.
   쉬운 예시로 들어보면 일반 색인(forward index)은 책의 목차와 같은 의미이고, 역색인(inverted index)은 책 가장 뒤의 단어 별 색인 페이지와 같다.
   - 만약 DB에서 “Trade”라는 문구가 포함된 문자열을 찾으려고 한다면 SQL에서는 %Trade% 라고 명확히 입력해야 검색이 가능할 것이다. trade, TRADE, trAde…. 등의 문자열은 직접 하나하나 명시하기 전에는 찾을 수 없을 것이다. ES의 역색인을 활용하면 대소문자 구분 없이 어떤 문구가 들어와도 찾을 수 있다.
---
--- 
### Elastic Search의 특징과 장단점
 - ES도 NoSQL의 일종
 - 분산처리를 통해 실시간성으로 빠른 검색이 가능, 특히 기존의 데이터로 처리하기 힘든 대량의 비정형 데이터 검색이 가능하며 전문 검색(full text) 검색과 구조 검색 모두를 지원한다.
 - 기본적으로는 검색엔진이지만 MongoDB나 Hbase와 같은 대용량 스토리지로도 활용이 가능하다.

 - 장점
  - 오픈소스 검색엔진이다. 활발한 오픈소스 커뮤니티가 ES를 끊임없이 개선하고 발전시키고 있다.
  - 전문검색
    - 내용 전체를 색인해서 특정 단어가 포함된 문서를 검색할 수 있다. 기능별, 언어별 플러그인을 적용할 수 있다.
  - 통계 분석
    - 비정형 로그 데이터를 수집하여 통계 분석에 활용할 수 있다. Kibana를 연결하면 실시간으로 로그를 분석하고 시각화할 수 있다.
  - Schemaless
    - 정형화되지 않은 문서도 자동으로 색인하고 검색할 수 있다.
  - RESTful API
    - HTTP기반의 RESTful를 활용하고 요청/응답에 JSON을 사용해 개발 언어, 운영체제, 시스템에 관계없이 다양한 플랫폼에서 활용이 가능하다.
  - Multi-tenancy
    - 서로 상이한 인덱스일지라도 검색할 필드명만 같으면 여러 인덱스를 한번에 조회할 수 있다.
  - Document-Oriented
    - 여러 계층 구조의 문서로 저장이 가능하며, 계층 구조로된 문서도 한번의 쿼리로 쉽게 조회할 수 있다.
  - 역색인(Inverted Index)
  - 확장성
    - 분산 구성이 가능하다. 분산 환경에서 데이터는 shard라는 단위로 나뉜다.
 - 단점
  - 완전 실시간은 아니다.
    - 색인된 데이터는 1초 뒤에나 검색이 가능하다. 내부적으로 commit과 flush같은 복잡한 과정을 거치기 때문.
  - Transaction Rollback을 지원하지 않는다.
    - 전체적인 클러스터의 성능 향상을 위해 시스템적으로 비용 소모가 큰 롤백과 트랜잭션을 지원하지 않는다. 
  - 데이터의 업데이트를 제공하지 않는다.
    - 업데이트 명령이 올 경우 기존 문서를 삭제하고 새로운 문서를 생성한다. 업데이트에 비해서 많은 비용이 들지만 이를 통해 불변성(Immutable)이라는 이점을 취한다.

--
--- 

### 참고
- [Elastic Search 기본 개념 잡기](https://github.com/exo-archives/exo-es-search)
- [Elastic Search는 무엇인가요?](https://www.elastic.co/kr/what-is/elasticsearch)
- [Elastic Search 기본 개념과 특징](https://jaemunbro.medium.com/elastic-search-%EA%B8%B0%EC%B4%88-%EC%8A%A4%ED%84%B0%EB%94%94-ff01870094f0)


