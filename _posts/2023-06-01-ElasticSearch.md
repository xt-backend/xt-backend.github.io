---
title: Elastic Search 관련자료
date: 2023-06-01 15:21:00 +0900
categories: [xt, Dev, ElasticSearch]
tags: [xt, ElasticSearch]
---

# Index
--- 
### ElasticSearch란?
### Elasticsearch는 어디에 사용되나요?
### Elasticsearch는 어떻게 작동하나요?
### Elasticsearch 인덱스는 무엇인가요?
### Logstash는 어디에 사용되나요?
### Elasticsearch를 사용하는 이유는 무엇인가요?

--- 

### Elasticsearch는 어디에 사용되나요?
  - Elasticsearch는 Apache Lucene( 아파치 루씬 ) 기반의 Java 오픈소스 분산 검색 엔진입니다. Elasticsearch를 통해 루씬 라이브러리를 단독으로 사용할 수 있게 되었으며, 방대한 양의 데이터를 신속하게, 거의 실시간( NRT, Near Real Time )으로 저장, 검색, 분석할 수 있습니다.
  - Elasticsearch는 검색을 위해 단독으로 사용되기도 하며, ELK( Elasticsearch / Logstatsh / Kibana )스택으로 사용되기도 합니다.
  - ELK 스택이란 다음과 같습니다.

    - Logstash
      - 다양한 소스( DB, csv파일 등 )의 로그 또는 트랜잭션 데이터를 수집, 집계, 파싱하여 Elasticsearch로 전달
    - Elasticsearch 
      - Logstash로부터 받은 데이터를 검색 및 집계를 하여 필요한 관심 있는 정보를 획득
    - Kibana
      -  Elasticsearch의 빠른 검색을 통해 데이터를 시각화 및 모니터링
* ![elasticsearch-1.png](/assets/img/posts/elasticsearch/elasticsearch-1.png)

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

### Elasticsearch는 어떻게 작동하나요?
  - 로그, 시스템 메트릭, 웹 애플리케이션 등 다양한 소스로부터 원시 데이터가 Elasticsearch로 흘러들어갑니다. 데이터 수집은 원시 데이터가 Elasticsearch에서 색인되기 전에 구문 분석, 정규화, 강화되는 프로세스입니다. Elasticsearch에서 일단 색인되면, 사용자는 이 데이터에 대해 복잡한 쿼리를 실행하고 집계를 사용해 데이터의 복잡한 요약을 검색할 수 있습니다. Kibana에서 사용자는 데이터를 강력하게 시각화하고, 대시보드를 공유하며, Elastic Stack을 관리할 수 있습니다.

### Elasticsearch 인덱스는 무엇인가요?
  - Elasticsearch 인덱스는 서로 관련되어 있는 문서들의 모음입니다. Elasticsearch는 JSON 문서로 데이터를 저장합니다. 각 문서는 일련의 키(필드나 속성의 이름)와 그에 해당하는 값(문자열, 숫자, 부울, 날짜, 값의 배열, 지리적 위치 또는 기타 데이터 유형)을 서로 연결합니다.
Elasticsearch는 역 인덱스라고 하는 데이터 구조를 사용하는데, 이것은 아주 빠른 풀텍스트 검색을 할 수 있도록 설계된 것입니다. 역 인덱스는 문서에 나타나는 모든 고유한 단어의 목록을 만들고, 각 단어가 발생하는 모든 문서를 식별합니다.
색인 프로세스 중에, Elasticsearch는 문서를 저장하고 역 인덱스를 구축하여 거의 실시간으로 문서를 검색 가능한 데이터로 만듭니다. 인덱스 API를 사용해 색인이 시작되며, 이를 통해 사용자는 특정한 인덱스에서 JSON 문서를 추가하거나 업데이트할 수 있습니다.

### Logstash는 어디에 사용되나요?
  - Elastic Stack의 핵심 제품 중 하나인 Logstash는 데이터를 집계하고 처리하여 Elasticsearch로 전송하는 데 사용됩니다. Logstash는 서버 사이드 오픈 소스 데이터 처리 파이프라인으로, 사용자는 이를 이용해 다양한 소스에서 동시에 데이터를 수집하고, 이를 강화하고 변환한 다음, Elasticsearch에서 색인되도록 할 수 있습니다.

### Kibana는 어디에 사용되나요?
  - Kibana는 Elasticsearch를 위한 시각화 및 관리 도구로서, 실시간 히스토그램, 선 그래프, 파이 차트, 지도 등을 제공합니다. Kibana에는 사용자가 자신의 데이터를 기반으로 사용자 정의한 동적 인포그래픽을 만들 수 있는 Canvas, 위치 기반 정보 데이터를 시각화하기 위한 Elastic Maps 같은 고급 애플리케이션도 포함됩니다.

### Elasticsearch를 사용하는 이유는 무엇인가요?
  - Elasticsearch는 빠릅니다. Elasticsearch는 Lucene을 기반으로 구축되기 때문에, 풀텍스트 검색에 뛰어납니다. Elasticsearch는 또한 거의 실시간 검색 플랫폼입니다. 이것은 문서가 색인될 때부터 검색 가능해질 때까지의 대기 시간이 아주 짧다는 뜻입니다. 이 대기 시간은 보통 1초입니다. 결과적으로, Elasticsearch는 보안 분석, 인프라 모니터링 같은 시간이 중요한 사용 사례에 이상적입니다.

  - Elasticsearch는 본질상 분산적입니다. Elasticsearch에 저장된 문서는 샤드라고 하는 여러 다른 컨테이너에 걸쳐 분산되며, 이 샤드는 복제되어 하드웨어 장애 시에 중복되는 데이터 사본을 제공합니다. Elasticsearch의 분산적인 특징은 수백 개(심지어 수천 개)의 서버까지 확장하고 페타바이트의 데이터를 처리할 수 있게 해줍니다.

  - Elasticsearch는 광범위한 기능 세트와 함께 제공됩니다. 속도, 확장성, 복원력뿐 아니라, Elasticsearch에는 데이터 롤업, 인덱스 수명 주기 관리 등과 같이 데이터를 훨씬 더 효율적으로 저장하고 검색할 수 있게 해주는 강력한 기본 기능이 다수 탑재되어 있습니다.

  - Elastic Stack은 데이터 수집, 시각화, 보고를 간소화합니다. Beats와 Logstash의 통합은 Elasticsearch로 색인하기 전에 데이터를 훨씬 더 쉽게 처리할 수 있게 해줍니다. Kibana는 Elasticsearch 데이터의 실시간 시각화를 제공하며, UI를 통해 애플리케이션 성능 모니터링(APM), 로그, 인프라 메트릭 데이터에 신속하게 접근할 수 있습니다.






