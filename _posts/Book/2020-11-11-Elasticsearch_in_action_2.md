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
    

- 쿼리 대신 필터 사용이 필요할 때 `match_all` 쿼리가 유용하다.
    - 도큐먼트 점수에 신경쓰지 않을 때 아래와 같이 사용
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "filtered": {
                        "query": {
                            "match_all": {}
                        },
                        "filter": {
                            // 필터 세부 내용
                        }
                    }
                },
            }'
        ```
    쿼리 요소는 이 경우 완벽하게 생략할 수 있다.

- query_string 쿼리
    - `AND`, `OR` 처럼 불린 연산자로 서로 다른 텀을 검색하고, `-`(마이너스) 연산을 사용해서 결과로부터 도큐먼트 제외하는걸 합칠 수 있다.
    - 가독성이 떨어지고 확장하기 어려운 단점
    - 웹 사이트 사용자에게 노출되면 위험하다는 점

- 텀 쿼리와 텀 필터
    - 텀 쿼리
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "term": {
                        "tags": "elasticsearch" // 내용
                    }
                },
            }'
        ```
    - 텀 필터
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "filtered": {
                        "query": {
                            "match_all": {}
                        },
                        "filter": {
                            "term": {
                                "tags": "elasticsearch" // 내용
                            }
                        }
                    }
                },
            }'
        ```
    - 텀 필터는 점수에 영향을 미치지 않고 텀을 포함하는 도큐먼트

- 텀즈 쿼리
    - jym 과 hadoop 중 어느 하나와 일치하는 도큐먼트를 찾는 예제
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "terms": {
                        "tags": ["jym", "hadoop"]
                    }
                },
            }'
        ```
    - 쿼리 일치 전에 도큐먼트에서 최소 개수의 텀 일치를 강제하려면, `minimum_should_match` 파라미터를 사용
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "terms": {
                        "tags": ["jym", "hadoop", "lucene"],
                        "minimum_should_match": 2
                    }
                },
            }'
        ```

#### 4.2.3 매치 쿼리와 텀 필터

- 매치 쿼리는 몇몇 서로 다른 방식으로 동작할 수 있다.
- 주요한 두 가지 기능은 `boolean`, `phrase`

- boolean 기능
    - 기본적으로 쿼리는 boolean 과 or 연산
    - "Elasticsearch Denver" 텍스트를 검색한다면 "Elasticsearch `OR` Denver" 로 검색
    - 아래처럼 연산자를 and 로 변경할 수 있다.
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "match": {
                        "name": {
                            "query": "Elasticsearch Denver",
                            "operator": "and"
                        }
                    }
                },
            }'
        ```

- phrase 기능
    - 각 단어의 위치에 따른 검색
    - leeway 는 토큰사이의 거리
        ```
            curl '..../{index}/_search' -d '{
                "query": {
                    "match": {
                        "name": {
                            "type": "phrase",   // phrase
                            "query": "enterprise london",
                            "slop": 1           // 텀간 leeway 거리를 가지도록
                        }
                    }
                },
            }'
        ```

- phrase_prefix 쿼리
    - phrase 로 검색하는 거에 더해서 마지막 텀에 프리픽스 매칭을 허용

- multi_match 로 다중 일치
    - 하나의 쿼리를 다중 필드에 검색

#### 4.3.1 bool 쿼리

- 몇 개의 쿼리라도 특정 부분이 must, should, must_not 로 데이터가 일치하는지 명시하게 하는 쿼리 구문을 이용해서 결합할 수 있다.

bool 쿼리 절 | 이진 연산 대응 | 의미 
---|:---:|:---:
must        | and | 일치
must_not    | not | 일치하지 않아야 함
should      | or  | 유사하게 일치하거나 그렇지 않을 수 있다, minimum_should_match 로 일치 개수 설정 가능

- bool filter 도 거의 동일
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "filtered": {
                    "query": {
                        "match_all": {}
                    },
                    "filter": {
                        "bool" {
                            "must" : [], // 내용
                            "should": [], // 내용
                            "must_not": [] // 내용
                        }
                    }
                }
            },
        }'
    ```

### 4.4 매치와 필터 쿼리를 넘어서서

#### 4.4.1 범위 쿼리와 필터

- **2012년 6월 1일 ~ 8월 1일** 범위 쿼리 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "range": {
                    "created_on": {
                        "gt": "2012-06-01",
                        "lt": "2012-09-01"
                    }
                }
            },
        }'
    ```
- 필터도 비슷하다.
- 범위쿼리는 문자열도 지원한다.
- 범위 쿼리를 만드는 데 99% 경우 필터가 옳은 선택이다.

#### 4.4.2 프리픽스 쿼리와 필터

- "query" 에 "prefix"

#### 4.4.3 와일드카드 쿼리

- e.g.)
    ```
        "query": {
            "wildcard": {
                "title": {
                    "wildcard": "ba*n"
                }
            }
        },
    ```

- 결코 가볍지 않다. 더 많은 작업이 필요하다.

### 4.5 존재하는 필드에 필터로 쿼리

#### 4.5.1 Exists 필터

- 특정 필드에 값을 가지는 도큐먼트
- `location.geolocation` 필드를 가진 도큐먼트만 반환하는 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "filtered": {
                    "query": {
                        "match_all": {}
                    },
                    "filter": {
                        "exists" {
                            "filed": "location.geolocation"
                        }
                    }
                }
            },
        }'
    ```

#### 4.5.2 Missing 필터

- 특정 필드에 값이 없는 도큐먼트
- `reviews` 필드가 없는 도큐먼트 검색하는 예제
    ```
        curl '..../{index}/_search' -d '{
            "query": {
                "filtered": {
                    "query": {
                        "match_all": {}
                    },
                    "filter": {
                        "missing" {
                            "filed": "reviews",
                            "existence": false,
                            "null_value": true
                        }
                    }
                }
            },
        }'
    ```
- `null_value` 는 해당 필드가 null 이라도 일치한다는 것인데, `existence` 는 확실하지가 않다.

#### 4.5.3 쿼리를 필터로 변환

- ES 는 어떤 쿼리도 필터로 전환하여 사용 할 수 있다.

- 캐시 쿼리
    - "query" 부분은 "fquery" 내부로 이동하고, "query"와 같은 레벨로 "_cache" 옵션
    - "_cache": true

- 표 4.3
    - ![일반 사용 사례에서 어느 쿼리 타입을 사용해야 하는가](https://github.com/Oraindrop/oraindrop.github.io/blob/master/assets/_img/EsInActionTable4-3.PNG?raw=true)

### 4.7 요약

- 필터는 점수 계산을 하지 않고 캐시로 쿼리 성능을 올릴 수 있다.


