# ElasticSearch? AWS OpenSearch? AWS CloudSearch?

- AWS에서 오픈소스 였던 ElasticSearch를 사용하고 있었으나 ElasticSearch가 특정 버전부터 유료화 되면서 AWS가 자체 검색 서비스를 만든 것이 AWS OpenSearch
- CloudSearch 더 사용하기 쉬운 버전

<img width="870" alt="Untitled" src="https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/681a6147-22bc-495d-a232-5ea2ae0a7c91">

[Amazon OpenSearch와 Elasticsearch 비교 | Elastic](https://www.elastic.co/kr/amazon-opensearch-service)

[칼럼 | 비난 세례 받았던 AWS의 오픈서치, 결국 성공했다](https://www.ciokorea.com/news/290191)

[Elasticsearch를 둘러싼 AWS와 Elastic의 대립 - OpsNow](https://www.opsnow.com/elasticsearch를-둘러싼-aws와-elastic의-대립/)

# 왜 Elastic Search를 사용하는가?

## ✳️ 용도

- 검색 엔진
- 로그 분석, 이벤트 분석, 성능 분석
- 머신 러닝

## ✳️ 엘라스틱 스택

## ✳️ 특징

- REST API 로 조작 가능
- 샤딩 제공
- JOIN, 트랜잭션은 미제공

## ✳️ 데이터 타입

- text, keyword, ip, 위치 정보(geo), 날짜 …

[Field data types | Elasticsearch Guide [8.9] | Elastic](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)

[7.2.6 위치 정보 - Geo](https://esbook.kimjmin.net/07-settings-and-mappings/7.2-mappings/7.2.6-geo)

## ✳️ 분석기

### 💫 Char Filter

- 입력받은 원본 텍스트 문자열을 추가, 변경, 제거

### 💫 Tokenizer

- 문자열을 토큰으로 분리

### 💫 Token Filter

- 토큰 추가, 수정, 제거
  
![Untitled 1](https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/01afdd6f-cbfa-4cba-a4df-065ce006511e)

- HTML Strip Char Filter : HTML 태그 제거
- standard Tokenizer : 띄어쓰기 기준으로 토큰 생성
- lowercase Token Filter : 영문자 토큰을 소문자로 치환
- stop Token Filter : 관사 제거
- snowball Token Filter : 복수형을 단수형 명사로 처리하는 필터

[All About Analyzers, Part One](https://www.elastic.co/kr/blog/found-text-analysis-part-1)

### 💫 역인덱스 (****Inverted Index) : Elastic Search가 검색이 빠른 이유****

- 분리된 토큰이 Document 정보를 가지고 있다
- RDB는 테이블 로우를 하나씩 검색하며 일치하는 키워드를 찾지만, **Elastic Search는 작은 단위의 토큰을 검색하며 토큰에 해당하는 Document를 찾는다.**

![Untitled 2](https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/a31cd36c-8f7a-4465-ac98-efc3b0bc46ff)

[6.1 역 인덱스 - Inverted Index](https://esbook.kimjmin.net/06-text-analysis/6.1-indexing-data)

# Elastic Local 설치하기

## ✳️ Docker에 이미지 Pull

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.6.0

8.6.0: Pulling from elasticsearch/elasticsearch
50b6068318e3: Pull complete
fc61f9d8fdc6: Pull complete
c035e50484c9: Pull complete
89732bc75041: Pull complete
44b8e4448631: Pull complete
80113011d9bc: Pull complete
3d5a7c9ac49a: Pull complete
d98bef40cfb8: Pull complete
6bc0fd7e7f4a: Pull complete
1b2135738b76: Pull complete
Digest: sha256:f7f8c619850471f9433d4cc43cdb65b629a222cff6527dc4e0dfeaa71211a138
Status: Downloaded newer image for docker.elastic.co/elasticsearch/elasticsearch:8.6.0
docker.elastic.co/elasticsearch/elasticsearch:8.6.0

What's Next?
  View summary of image vulnerabilities and recommendations → docker scout quickview docker.elastic.co/elasticsearch/elasticsearch:8.6.0
```

## ✳️ Docker Network 생성

```bash
docker network create elastic

5288030922c5aaaa50af5ee72918943fc0416d7acf6f7f57bd6f8b4090f58e8d
```

## ✳️ Elastic Search 실행하기

- 컨테이너 이름: es01
- ElasticSearch 용 포드: 9200

```bash
docker run --name es01 --net elastic -p 9200:9200 -it docker.elastic.co/elasticsearch/elasticsearch:8.6.0

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.

ℹ️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
  A*euESgBAufgO3iSb1hJ

ℹ️  HTTP CA certificate SHA-256 fingerprint:
  8dd82662da508f48f7e606be785801834d3ad86d473e7d5669fb1a89e90e74e6

ℹ️  Configure Kibana to use this cluster:
• Run Kibana and click the configuration link in the terminal when Kibana starts.
• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjYuMCIsImFkciI6WyIxNzIuMTguMC4yOjkyMDAiXSwiZmdyIjoiOGRkODI2NjJkYTUwOGY0OGY3ZTYwNmJlNzg1ODAxODM0ZDNhZDg2ZDQ3M2U3ZDU2NjlmYjFhODllOTBlNzRlNiIsImtleSI6ImdIcUxwSW9CZ1p0RDY2dzBpSmJGOktGM210VE5GUXBHckk0Qmp2ckdIc1EifQ==

ℹ️ Configure other nodes to join this cluster:
• Copy the following enrollment token and start new Elasticsearch nodes with `bin/elasticsearch --enrollment-token <token>` (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjYuMCIsImFkciI6WyIxNzIuMTguMC4yOjkyMDAiXSwiZmdyIjoiOGRkODI2NjJkYTUwOGY0OGY3ZTYwNmJlNzg1ODAxODM0ZDNhZDg2ZDQ3M2U3ZDU2NjlmYjFhODllOTBlNzRlNiIsImtleSI6ImducUxwSW9CZ1p0RDY2dzBpSmJGOnVXT2ZIY3NmUjlxMkk0WXc5Q210M0EifQ==

  If you're running in Docker, copy the enrollment token and run:
  `docker run -e "ENROLLMENT_TOKEN=<token>" docker.elastic.co/elasticsearch/elasticsearch:8.6.0`
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## ✳️ 보안 관련 토큰 로그

- Elastic Search를 실행하면 터미널에 로그가 보인다.
- 비밀번호, 키바나 토큰, 노드 등록 토큰 → 별도의 에디터에 저장해 두자!

## ✳️ 비밀번호 초기화

- Elastic Search를 설치하면 기본 계정 elastic이 생성된다.

```bash
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

## ✳️ 보안 인증 파일을 로컬에 복제

- 로컬에서 curl로 Elastic Search 사용시 필요하다

```bash
docker cp es01:/usr/share/elasticsearch/config/certs/http_ca.crt .
```

## ✳️ 접속 시 인증

```bash
curl --cacert http_ca.crt -u elastic https://localhost:9200

Enter host password for user 'elastic':
{
  "name" : "18ebc99549f2",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "3iqqsCo5Q1mRc2QgXSlyfA",
  "version" : {
    "number" : "8.6.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "f67ef2df40237445caa70e2fef79471cc608d70d",
    "build_date" : "2023-01-04T09:35:21.782467981Z",
    "build_snapshot" : false,
    "lucene_version" : "9.4.2",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

## ✳️ Elastic Search 가 잘 떳는지 확인하기

```bash
curl --cacert http_ca.crt -u elastic -X GET "https://localhost:9200/_cluster/health?pretty"

Enter host password for user 'elastic':
{
  "cluster_name" : "docker-cluster",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

# Kibana 설치

## ✳️ 로컬 설치

```bash
docker pull docker.elastic.co/kibana/kibana:8.6.0

8.6.0: Pulling from kibana/kibana
50b6068318e3: Already exists
bfa22ec5911b: Pull complete
781a9bfd2e06: Pull complete
3f56b2c52e35: Pull complete
79890c11b59b: Pull complete
89732bc75041: Pull complete
236c786f33cb: Pull complete
1bd5b994c04b: Pull complete
ff456d754c4e: Pull complete
fe8bc3e75fe7: Pull complete
8402b5ead178: Pull complete
c30b624e4669: Pull complete
aa0e997605a5: Pull complete
d9fa01ed6daf: Pull complete
Digest: sha256:ca2cdc6835aedc43c100d5e2f387cac50bde73a0734c6553257171cd8f1c0eea
Status: Downloaded newer image for docker.elastic.co/kibana/kibana:8.6.0
docker.elastic.co/kibana/kibana:8.6.0

What's Next?
  View summary of image vulnerabilities and recommendations → docker scout quickview docker.elastic.co/kibana/kibana:8.6.0
```

## ✳️ 로컬 실행

```bash
docker run --name kibana --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.6.0

[2023-09-17T19:36:08.824+00:00][INFO ][node] Kibana process configured with roles: [background_tasks, ui]
[2023-09-17T19:36:14.217+00:00][INFO ][plugins-service] Plugin "cloudChat" is disabled.
[2023-09-17T19:36:14.217+00:00][INFO ][plugins-service] Plugin "cloudExperiments" is disabled.
[2023-09-17T19:36:14.217+00:00][INFO ][plugins-service] Plugin "cloudFullStory" is disabled.
[2023-09-17T19:36:14.217+00:00][INFO ][plugins-service] Plugin "cloudGainsight" is disabled.
[2023-09-17T19:36:14.221+00:00][INFO ][plugins-service] Plugin "profiling" is disabled.
[2023-09-17T19:36:14.269+00:00][INFO ][http.server.Preboot] http server running at http://0.0.0.0:5601
[2023-09-17T19:36:14.292+00:00][INFO ][plugins-system.preboot] Setting up [1] plugins: [interactiveSetup]
[2023-09-17T19:36:14.293+00:00][INFO ][preboot] "interactiveSetup" plugin is holding setup: Validating Elasticsearch connection configuration…
[2023-09-17T19:36:14.311+00:00][INFO ][root] Holding setup until preboot stage is completed.

i Kibana has not been configured.

Go to http://0.0.0.0:5601/?code=249056 to get started.
```

## ✳️ Kibana 비밀번호 초기화

```bash
docker exec -it /usr/share/elasticsearch/bin/elasticsearch-reset-password --username kibana_system
```

## ✳️ Kibana 홈 접속

- 설치된 Elastic Search 주소 입력
    - http://0.0.0.0:5601/?code=249056

<img width="576" alt="Untitled 3" src="https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/08cd75df-7380-4f5a-b313-7274848ddb8e">    
<img width="433" alt="Untitled 4" src="https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/f02683c0-f06c-4c31-ba67-75d5cee211a3">
<img width="466" alt="Untitled 5" src="https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/cf09fe6f-5854-4a01-88aa-e321bc54eb68">
<img width="1659" alt="Untitled 6" src="https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/b5ddfa31-1895-4475-bbec-1f98fbe03812">

---

# 데이터 적재하기 (Console 명령어)

## ✳️ Index 관리

- Elastic Search에서 Index란?
    - Document를 모은 논리적 구조
    - RDB의 테이블에 해당

### 💫 Index 추가

```json
PUT {인덱스 명}
```

### 💫 Index 조회

```json
GET {인덱스 명}
```

### 💫 Index 삭제

```json
DELETE {인덱스 명}
```

## ✳️ Document 관리

- 데이터가 저장되는 기본 단위
    - RDB의 튜플에 해당
- Document는 Index에 추가된다
- 인덱싱이란? Document를 인덱스에 추가하는 것

### 💫 PUT : Document 추가

- 타입을 기입하지 않아도 자동으로 타입을 식별하고 지정해 준다(이를 Dynamic Mapping이라 한다.)

```bash
// Document 추가

PUT {인덱스명}/_doc/{도큐먼트 식별자}
{
	"name": "찐빵이",
	"price": 3000
}
```

### 💫 PUT : Document 수정

- 수정할 필드만 입력해서 덮어쓰는 방식

```bash
PUT {인덱스명}/_doc/{도큐먼트 식별자}
{
	"price": 6000
}
```

### 💫 GET : Document 조회

```bash
GET {인덱스명}/_doc/{도큐먼트 식별자}
```

### 💫 Bulk Document 추가

- 한번에 여러 Document를 추가
- 포맷은 NDJSON (New Line Delimited JSON)
    - NDJSON이란? 다수의 JSON을 줄바꿈 물자열로 구분하는 포맷

```json
POST _bulk
{ "index": { "_index": "인덱스명", "_id": "도큐먼트 식별자" } }
{ "필드명1": "필드값1", ..., "필드명1": "필드값1" }
{ "index": { "_index": "인덱스명", "_id": "도큐먼트 식별자" } }
{ "필드명1": "필드값1", ..., "필드명1": "필드값1" }
...
```

# 데이터 조회하기 (Console 명령어)

## ✳️ Full Text Queries (전문 쿼리)

- 일반적인 검색 엔진과 유사
- 검색 키워드를 분석하고 토큰화 한다.

### 💫 Match Query

- `_search` : 검색 API
- `_source` : 해당 필드만 응답해 준다 (RDB SQL의 SELECT 절을 생각하면 된다)
- 검색 키워드를 토큰화 하여 OR 조건으로 인식한다.
    - e.g. “자바의 정석”을 검색하면 {”자바의”, “정석”}으로 나누고 OR 조건으로 검색한다. 즉, 두 키워드 중 하나의 키워드만 일치/포함해도 검색 결과에 노출된다.

```json
# March Query

GET {인덱스 명}/_search
{
	"_source": ["필드명"],       # 없으면 전체 필드를 가져온다
	"query": {
		"match": {
			"필드명": "검색어",
			"operator": "and|or"   # 생략하면 or 조건으로 검색된다.
		}
	}
}
```

### 💫 Match Pharse Query

- 구(Pharse)를 검색하는데 사용
    - 구(Pharse)란? 동사 이외의 단어가 두 개 이상으로 이루어진 문장
    - 단어의 순서가 중요하다.
    - Match Query의 and 조건과 비슷하다.

```json
# March Pharse Query

GET {인덱스 명}/_search
{
	"_source": ["필드명"],       # 없으면 전체 필드를 가져온다
	"query": {
		"match_pharse": {
			"필드명": "검색어"
		}
	}
}
```

### 💫 Mulit Match Query

- 정확히 어떤 필드에서 찾아야 하는지 모르는 경우
- 와일드 카드(*)를 사용해 모든 필드에서 검색할 수 있다

```json
# Mulit Match Query

GET {인덱스 명}/_search
{
	"_source": ["필드명1", "필드명2", "필드명3"],       # 없으면 전체 필드를 가져온다
	"query": {
		"multi_match": {
				"query": "검색어",
				"field": ["필드명1", "필드명2"]
		}
	}
}
```

## ✳️ Term Level Queries (용어 수준 쿼리)

- 검색 키워드를 토큰화 하지 않는다.
    - 입력한 키워드와 동일한 값을 찾아 온다
- keyword 타입에 맵핑된 필드에 사용한다.

### 💫 Term Query

```json
# Term Query

GET {인덱스 명}/_search
{
	"_source": ["필드명"],       # 없으면 전체 필드를 가져온다
	"query": {
		"term": {
			"필드명.keyword": "검색어"
		}
	}
}
```

### 💫 Terms Query

- 대소문자 구분
- 복수개의 용어를 검색할 수 있다.

```json
# Terms Query

GET {인덱스 명}/_search
{
	"_source": ["필드명", "필드명.keyword"],
	"query": {
		"terms": {
			"필드명.keyword": [ "검색어1", "검색어2" ]
		}
	}
}
```

## ✳️ 기타 Queries

### 💫 Range Query

- 날짜, IP 주소, 숫자 필드에서 사용 가능
- 검색 조건에 주어진 범위에 포함된 데이터만 검색한다.
- 조건
    - gte (greater then equals) :  크거나 같다
    - gt (greater then) : 크다
    - lte (less then equals) : 작거나 같다
    - lt (less then) : 작다

```json
# Range Query

GET {인덱스 명}/_search
{
	"_source": ["필드명1", "필드명2", "필드명3"],
	"query": {
		"range": {
			"필드명": {"gte": 4, "lte": 10}
		}
	}
}
```

### 💫 Bool Query

- 쿼리를 조합해서 사용
- 검색 타입
    - must : true인 데이터를 검색
    - must_not : false인 데이터를 검색
    - should
        - 단독 사용 가능, true인 데이터 검색
        - 다른 타입과 함께 사용하면 스코어를 높이는데 활용
        - 다른 쿼리와 사용하면 OR 조건
    - filter
        - 불필요한 데이터를 필터링 할 때 사용
        - yes/no 형태의 필터를 제공
        - 쿼리 스코어 무시, 속도 빠름, 캐싱 가능

```json
# Bool Query

GET {인덱스 명}/_search
{
	"_source": ["필드명1", "필드명2", "필드명3", "필드명4"],
	"query": {
		"bool": {
			"filter" : [
				{ "term": { "필드명1": "검색어"} }
			],
			"must": [
				{ "term": {"필드명2": "검색어"} }
			],
			"must_not": [
				{ "range": { "필드명3": { "lte": 4 } } }
			],
			"should": [
				{ "term": { "필드명4": "검색어 검색어" } }
			]
		}
	}
}
```

---

# 한국어 형태소 분석기 설치

[6.7.2 노리 (nori) 한글 형태소 분석기](https://esbook.kimjmin.net/06-text-analysis/6.7-stemming/6.7.2-nori)

[nori_tokenizer | Elasticsearch Plugins and Integrations [8.9] | Elastic](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-nori-tokenizer.html)

[[처음부터 시작하는 elastic] 2-5 노리 (nori) 한글 형태소 분석기](https://www.youtube.com/watch?v=haAih_Ek5jg&list=PLhFRZgJc2afp0gaUnQf68kJHPXLG16YCf&index=18)

[Elasticsearch를 검색 엔진으로 사용하기(1): Nori 한글 형태소 분석기로 검색 고도화 하기 - 하나몬](https://hanamon.kr/elasticsearch-검색엔진-nori-형태소-분석기-검색-고도화-방법/)

## ✳️  Elastic Search Docker Container 접속

```bash
docker exec -it 18ebc99549f293caaa30bf9050f5e6425e085aac614f4e2f0ddcf29829e08321 /bin/bash
```

## ✳️  Nori 설치

```bash
$ /bin/elasticsearch-plugin install analysis-nori

-> Installing analysis-nori
-> Downloading analysis-nori from elastic
[=================================================] 100%??
-> Installed analysis-nori
-> Please restart Elasticsearch to activate any plugins installed
```

## ✳️  Nori 형태소 적용

```bash
# locations-01 인덱스의 세팅, 맵핑(필드) 정보 보기
GET locations-01
GET locations-01/_settings
GET locations-01/_mapping

# locations-01 인덱스의 데이터에서 검색하기
GET locations-01/_search
{
	"_source": ["si_do"],
	"query": {
		"match": {
			"si_do": "서울특별시"
		}
	}
}

# 기본 설정된 토크나이저로 검색
# { 서울특별시, 강남구, 역삼1동 } 키워드로만 검색된다.
# 유사어 검색이 불가하다
GET _analyze
{
  "tokenizer": "standard",
  "text": [
    "서울특별시 강남구 역삼1동"
  ]
}

# Ngram 토크나이저로 검색
# { 서, 서울, 울, 울특, 특, 특별, 별, 별시, 시, ... }
# 너무 많고 의미없는 단위의 토큰이 저장된다.
GET _analyze
{
  "tokenizer": "ngram",
  "text": [
    "서울특별시 강남구 역삼1동"
  ]
}

# Nori 토크나이저로 검색
# { 서울, 특별, 시, 강남, 구, 역삼, 1, 동 }
# 의미있는 형태소 단위의 토큰이 저장된다.
GET _analyze
{
  "tokenizer": "nori_tokenizer",
  "text": [
    "서울특별시 강남구 역삼1동"
  ]
}

#-----------------------------------------

# Nori 토크나이저가 적용된 인덱스 생성
PUT nori_location
{
  "settings": {
    "index": {
      "analysis": {
        "tokenizer": {
          "my_nori_tokenizer": {
            "type": "nori_tokenizer",
            "decompound_mode": "mixed",
            "discard_punctuation": "false" // 문장 부호 제거하지 않도록 설정한다.
          }
        },
        "analyzer": {
          "my_nori_analyzer": {
            "type": "custom",
            "tokenizer": "my_nori_tokenizer", 
            // lowercase: 알파벳 소문자로 통일 시킨다.
            // stop: "the", "a", "an", "and" 와 같은 단어 검색에서 제외
            "filter": ["lowercase", "stop"],
            "char_filter": ["html_strip"] // html_strip: HTML 태그 제거
          }
        }
      }
    }
  },
  "mappings" : {
    "properties" : {
      "h_code": {
        "type": "long"
      },
      "si_do": {
        "type" : "text",
        "analyzer": "my_nori_analyzer"
      },
      "si_gun_goo": {
        "type" : "text",
        "analyzer": "my_nori_analyzer"
      },
      "eub_myeon_dong": {
        "type" : "text",
        "analyzer": "my_nori_analyzer"
      },
      "cancellation_date": {
        "type": "date",
        "format": "iso8601"
      },
      "create_date": {
        "type": "date",
        "format": "iso8601"
      },
      "@timestamp": {
        "type": "date"
      }
    }
  }
}

# 잘 만들어 졌는지 확인하기
GET nori_location
GET nori_location/_settings
GET nori_location/_mapping

# 데이터 삽입(_reindex: locations-01에 있는 데이터를 nori_location에 복사)
POST _reindex
{
  "source": {
    "index": "locations-01"
  },
  "dest": {
    "index": "nori_location"
  }
}

# 검색해 보기
GET nori_location/_search
{
	"_source": ["si_do"],
	"query": {
		"match": {
			"si_do": "서울"
		}
	}
}
```

# 검색 API 서비스 만들기

## ✳️  검색 서비스 아키텍처

### 💫 검색 엔진을 도메인에 직접 사용하는 경우

![es01](https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/fe25a9be-de1a-4f1a-afd1-a0ccd35c477f)

- 도메인 시스템과 검색 시스템의 강한 의존성
- 검색 엔진을 변경, 유지보수 하기 어려움

### 💫 별도의 검색 API를 추가한다

![es02](https://github.com/CodeSquad-2023-BE-Study/Flytrap-Study/assets/86359180/70b84df5-4039-42dd-9474-87ff6b6a632c)

- 도메인 시스템과 검색 시스템의 의존성을 낮춘다

## ✳️  Spring Boot 프로젝트

- 테스트 환경
    - Spring boot 3.1.3
    - Java 17 version

### 💫 https 접속을 위한 fingerprint 생성

```bash
openssl x509 -fingerprint -sha256 -noout -in ./http_ca.crt

sha256 Fingerprint=8D:D8:26:62:DA:50:8F:48:F7:E6:06:BE:78:58:01:83:4D:3A:D8:6D:47:3E:7D:56:69:FB:1A:89:E9:0E:74:E6
```

### 💫 Gradle 의존성 추가

```
implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
```

### 💫 application.yml 설정 추가

```
spring:
  elasticsearch:
    rest:
      host: localhost
      port: 9200
    fingerprint: "8D:D8:26:62:DA:50:8F:48:F7:E6:06:BE:78:58:01:83:4D:3A:D8:6D:47:3E:7D:56:69:FB:1A:89:E9:0E:74:E6"
    account: "elastic"
    password: "A*euESgBAufgO3iSb1hJ"

es-index:
  nori_location-index: "nori_location"
```

### 💫 ElasticSearch Configuration 추가

```java
import javax.net.ssl.SSLContext;

import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.elasticsearch.client.RestClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import co.elastic.clients.elasticsearch.ElasticsearchClient;
import co.elastic.clients.json.jackson.JacksonJsonpMapper;
import co.elastic.clients.transport.ElasticsearchTransport;
import co.elastic.clients.transport.TransportUtils;
import co.elastic.clients.transport.rest_client.RestClientTransport;

@Configuration
public class ElasticsearchClientConfig {

	@Value("${spring.elasticsearch.rest.host}")
	String host;
	@Value("${spring.elasticsearch.rest.port}")
	int port;

	@Value("${spring.elasticsearch.fingerprint}")
	String fingerprint;
	@Value("${spring.elasticsearch.account}")
	String account;
	@Value("${spring.elasticsearch.password}")
	String password;

	@Bean
	public ElasticsearchClient elasticsearchClientWithSSL() {
		// SSL 통신
		SSLContext sslContext = TransportUtils
			.sslContextFromCaFingerprint(fingerprint);

		// 인증
		BasicCredentialsProvider credsProv = new BasicCredentialsProvider();
		credsProv.setCredentials(
			AuthScope.ANY, new UsernamePasswordCredentials(account, password)
		);

		RestClient restClient = RestClient
			.builder(new HttpHost(host, port, "https"))
			.setHttpClientConfigCallback(hc -> hc
				.setSSLContext(sslContext)
				.setDefaultCredentialsProvider(credsProv)
			)
			.build();

		// 전송 객체와 클라이언트 생성
		ElasticsearchTransport transport = new RestClientTransport(restClient, new JacksonJsonpMapper());
		return new ElasticsearchClient(transport);
	}
}
```

### 💫 Domain 클래스 생성

```java
public class Location {

	private String si_do;
	private String si_gun_goo;
	private String eub_myeon_dong;

	private Location() {}

	public Location(String si_do, String si_gun_goo, String eub_myeon_dong) {
		this.si_do = si_do;
		this.si_gun_goo = si_gun_goo;
		this.eub_myeon_dong = eub_myeon_dong;
	}

	public String getSi_do() {
		return si_do;
	}

	public String getSi_gun_goo() {
		return si_gun_goo;
	}

	public String getEub_myeon_dong() {
		return eub_myeon_dong;
	}

	public String searchFullName() {
		return String.format("%s %s %s", si_do, si_gun_goo, eub_myeon_dong);
	}
}
```

### 💫 Controller 생성

```java
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.example.es_demo.dmain.Location;

import co.elastic.clients.elasticsearch.ElasticsearchClient;
import co.elastic.clients.elasticsearch.core.SearchResponse;
import co.elastic.clients.elasticsearch.core.search.Hit;
import co.elastic.clients.elasticsearch.core.search.SourceConfig;
import co.elastic.clients.elasticsearch.core.search.TotalHits;
import co.elastic.clients.elasticsearch.core.search.TotalHitsRelation;

@RestController
public class ElasticSearchController {

	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	private final ElasticsearchClient elasticsearchClient;

	@Value("${es-index.nori_location-index}")
	private String searchIndex;

	public ElasticSearchController(ElasticsearchClient elasticsearchClient) {
		this.elasticsearchClient = elasticsearchClient;
	}

	@GetMapping("/api/search")
	public ResponseEntity<?> findAll(@RequestParam String keyword) throws IOException {

		logger.info(">>> 🔎 키워드 : {}", keyword);

		SourceConfig sourceConfig
			= SourceConfig.of(
				sc -> sc.filter(
					sf -> sf.includes("si_do", "si_gun_goo", "eub_myeon_dong")
				)
			);

		SearchResponse<Location> response
			= elasticsearchClient.search(s -> s
				.index(searchIndex)
				.source(sourceConfig)
				.query(q -> q
					.match(t -> t
						.field("si_do")
						.field("si_gun_goo")
						.field("eub_myeon_dong")
						.query(keyword)
					)
				), Location.class
		);

		TotalHits totalHits = response.hits().total();
		assert totalHits != null;
		logger.info(">>> 🔢 일치한 결과: {}", totalHits.value());

		List<Hit<Location>> hits = response.hits().hits();
		List<Location> locations = new ArrayList<>();
		for (Hit<Location> hit: hits) {
			Location location = hit.source();
			locations.add(0, location);
			assert location != null;
			logger.info(">>> ✅ 결과: {}", location.searchFullName());
		}

		return ResponseEntity.ok(locations);
	}

}
```

### 💫 API 결과

```java
### url.http
http://localhost:8080/api/search?keyword=서울 역삼

### 결과
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Mon, 18 Sep 2023 19:07:20 GMT
Keep-Alive: timeout=60
Connection: keep-alive

[
  {
    "si_do": "서울특별시",
    "si_gun_goo": "강남구",
    "eub_myeon_dong": "역삼2동"
  },
  {
    "si_do": "서울특별시",
    "si_gun_goo": "강남구",
    "eub_myeon_dong": "역삼1동"
  },
  {
    "si_do": "경기도",
    "si_gun_goo": "용인시 처인구",
    "eub_myeon_dong": "역삼동"
  },
  {
    "si_do": "경기도",
    "si_gun_goo": "용인시",
    "eub_myeon_dong": "역삼동"
  }
]
Response file saved.
> 2023-09-19T040720.200.json

Response code: 200; Time: 13ms (13 ms); Content length: 243 bytes (243 B)
```

---

# 부족한 점

- 다른 검색 서비스와 비교 분석
- Kibana 대시보드에서 데이터 분석 사용하기

---

# 궁금했던 거

## Docker pull 하면 나오는 Digest가 뭔지?

[117. [Docker] Docker에서 이미지의 Digest 에 대한 간단한 고찰](https://blog.naver.com/alice_k106/221149596996)

## Elastic Search 요금제 어떻게 보는건지?

[Elasticsearch License](https://bonohubby.com/entry/Elasticsearch-License)

### AWS Elastic Search

[AWS elasticsearch](https://velog.io/@broccoliindb/AWS-elasticsearch)
