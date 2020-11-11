---
layout: post
title:  "도서 - Elasticsearch in action (4~작성중)"
categories: Book
author : choising
tags: java, book, es
---

# Elasticsearch in action (139p~)

## 4장 데이터 검색

### 4.1 검색 요청의 구조

#### 4.1.1 검색 범위 지정하기

- `_search` ...
    - 전체 클러스터 검색

- `{index}/_search` ...
    - index 에서 검색

- `{index}/event/_search` ...
    - index 에서 event type 검색

- `_all/event/_search`, `*/event/_search` ...
    - 전체 index 에서 event type 검색

- `{index},other/event,group/_search` ...
    - index 와 다른 인덱스에서 이벤트 및 그룹 타입 검색

- `+get-toge*,-{index}/_search` ...
    - 명시된 index 제외, get-toge에 해당하는 모든 인덱스에서 검색

- 다중 인덱스 검색하는 alias 도 사용할 수 있다.
    - 당연히 최고 성능을 내려면 최소한의 인덱스를 타도록 하는게 좋다.


#### 4.1.2 검색 요청의 기본 구성 요소

- `query`
    - 검색 요청에 있어 가장 중요한 구성 요소
    - 점수 기반으로 최적의 도큐먼트를 반환하거나 원치 않는 도큐먼트를 걸러내도록 설정
    - DSL 쿼리와 DSL 필터를 사용해서 구성
    - e.g.) `_search?q=title:elasticsearch`
        - title이 elasticsearch 단어와 일치하는 도큐먼트를 찾겠다.

- `size`
    - 반환할 도큐먼트 개수

- `from`
    - size 와 함께 페이징
    - e.g.) `_search?from=7&size=5`
        - from 7, size 5 라면 8,9,10,11,12 번째 결과를 반환한다.

- `_source`
    - _source 필드를 어떻게 반환할 것인가
    - _source 하면 완전한 _source를 반환
    - 설정으로 반환되는 필드를 걸러낼 수 있다.
    - 전체 내용이 필요하지 않을 때 적절히 사용
    - e.g.) `_search?sort=date:asc&_source=title,date`

        ```
            ...
            "_source": {
                "date" :
                "title" :
            },
            ...
        ```
        날짜 오름차순 순으로 정렬한 결과에서 최초 10건을 response에 title 과 date 필드만 포함

- `sort`
    - e.g.) `_search?sort=date:asc`
        - 날짜 오름차순으로 정렬한 결과 중 최초 10개를 반환한다.


#### 4.1.3 본문 기반 검색 요청 사용하기

- `from`, `size` 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "match_all": {}
            },
            "from": 10,
            "size": 10
        }'
    ```

- `_source` 필터링 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "match_all": {}
            },
            "_source": ["name", "date"]
        }'
    ```

    와일드카드도 사용 가능 (name, nation 필드 둘 다 반환할 때) `_source: "na*"` 이렇게 할 수 도 있음

- response 에 포함할 필드(include), 제외할 필드(exclude) 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "match_all": {}
            },
            "_source": {
                "include": ["location.*", "date"],
                "exclude": ["location.geolocation"]
            }
        }'
    ```

- 순서 정렬
    - 지정하지 않으면, 일치한 도큐먼트를 `_score` 값의 내림차순으로 정렬
    - 생성일 기준 가장 오래된 것으로 시작해서, 그룹 이름 기준으로, 마지막으로 `_score` 결과로 정렬 결과를 반환하는 예제

        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "match_all": {}
                },
                "sort": [
                    {"created_on": "asc"},
                    {"name": "desc"},
                    "_score"
                ]
            }'
        ```


### 4.2 쿼리와 필터 DSL

- match query 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "match": {
                    "title": "hadoop"
                }
            },
        }'
    ```

- 쿼리가 특정 텀으로 점수 계산하는 것과 달리, 검색에서 필터는 **이 도큐먼트가 이 쿼리와 일치 하는가** 하는 단순 이진법의 예/아니요 답변만을 반환한다.
    - 필터는 일반 쿼리를 사용하는 것보다 더 빠를 수 있고, 캐시할 수 있다.
    - 필터 쿼리 예제
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "filtered": {
                        "query": {
                            "match": {
                               "title": "hadoop"
                            }
                        },
                        "filter": {
                            "term": {
                                "host": "andy"
                            }
                        }
                    }
                },
            }'
        ```
        일반 쿼리 필터 title hadoop, 텀 필터 host andy

    - ES 는 필터를 캐시 해야 할 지에 관해 수동으로 지정하는 기능을 제공한다.
        - 음 살짝 db index 비슷한 기분?
    
    
    

    
