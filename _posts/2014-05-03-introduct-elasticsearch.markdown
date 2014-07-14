---
layout: post
title:  "Introduce elasticsearch"
date:   2014-05-04 17:01:00
categories: elasticsearch
---

### elasticsearch 소개 및 활용

사실, 고백하건데 elastic-이라는 prefix가 붙어서 AWS 서비스인 줄 알았었다. -_-; 
아무튼, DB 검색으로는 속도 상으로 한계도 있고 복잡한 조건을 검색하는데 있어 부담이 될 수 있는데, elasticsearch를 이용하여 데이터를 쌓으면 검색하기가 매우 쉬워진다. 

Schema-free라고 해서, 검색하는 데 있어 별다른 문법이 없고 JSON 타입으로 검색하면 된다. 그리고 수집된 데이터에 대해 정상적으로 검색하는지에 대한 테스트는 Chrome Plugin을 사용하면 편리하다. 더 자세한 내용은 아래 포스트를 참고하도록 하자. 
https://www.found.no/foundation/Sense-Elasticsearch-interface/

자, 그럼 elasticsearch 설치부터 사용법까지 한 번에 가보도록 하겠다.

1. 설치:  매우 쉽다. 다운로드 받아서 실행하면 끝이다. 
	- http://www.elasticsearch.org/download 다운로드 받는다. 
    - zip/tar.gz 다운로드 후 압축을 푼다. 
    - 압축을 해제한 디렉토리 안으로 접근하여 bin/elasticsearch 로 띄우면 끝이다.
    	- Background Process로 실행하고 싶으면 bin/elasticsearch -d 
    - 기본적으로 node간 통신을 위해 9300-9400포트를, 데이터 통신을 위해 9200 포트를 사용하고 아래에서 더 자세한 내용을 다루도록 하겠다. 
    
2. 설정: config/elasticsearch.xml
	- YAML 문법을 따르고 있다. 
	- network 설정을 통해 node 간 통신을 위해 binding될 주소를 적어준다. 
<pre><code>network :
		host : 10.0.0.4
 </code></pre>
 
	- 로그 및 데이터 경로도 별도로 지정할 수 있다. 
<pre><code>path:
		logs: /var/log/elasticsearch
		data: /var/data/elasticsearch
</code></pre>
	
###  References
- configuration : http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/setup-configuration.html


3. 사용
- elasticsearch 는 JSON, thrift, memcached 기반으로 움직이고 있다. 
- restful API 형식으로 질의를 수행하며 아래와 같은 API를 제공한다. 
	- search : 검색
	- index : 추가
	- get : 조회
	- delete : 삭제
	- update : 갱신 
- 검색을 수행하기 위해 기본적인 데이터 이외에도 아래와 같은 API도 추가로 제공하고 있다. 
	- bulk : 일괄 수행
	- multiple get 
	
- restful api 사용할 때 처럼, 검색 시 GET, 데이터 추가 시 POST, 수정 시 PUT, 삭제 시 DELETE Method로 전송하면 된다. 
	- search API를 예로 들어 보겠다.
<pre><code>
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?routing=kimchy' -d '{
    "query": {
        "filtered" : {
            "query" : {
                "query_string" : {
                    "query" : "some query string here"
                }
            },
            "filter" : {
                "term" : { "user" : "kimchy" }
            }
        }
    }
}
</code></pre>

- 위와 같이 실행했을 때 결과는 아래와 같다. 
<pre><code>
{
   "took": 6,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 56,
      "max_score": null,
      "hits": []
   }
}
</code></pre>

- _shards의 정보와 검색에 매치된 데이터를 돌려준다. hits key 안에 입력한 데이터를 리턴해준다. 
- 검색 쪽도 정리하려니 양이 상당히 많다. 일단 이정도로 넣고 다시 수정하던가 해야겠다. 