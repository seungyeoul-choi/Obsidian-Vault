---
tags:
  - clippings
  - kafka
  - kafka-connect
  - mysql
  - database
tagged: true
tagged_at: 2026-08-21
ingested: false
---

## Kafka Connect란?

먼저 Kafka는 Producer와 Consumer를 통해 데이터 파이프라인을 만들 수 있다. 예를 들어 A서버의 DB에 저장한 데이터를 Kafka Producer/Consumer를 통해 B서버의 DB로도 보낼 수 있다. 이러한 파이프라인이 여러개면 매번 반복적으로 파이프라인을 구성해야줘야한다. KafkConnect는 이러한 반복적인 파이프라인 구성을 쉽고 간편하게 만들 수 있게 만들어진 Apache Kafka 프로젝트중 하나다.

![](https://blog.kakaocdn.net/dna/cF4X3U/btrhVsFQhBK/AAAAAAAAAAAAAAAAAAAAABlGO-3sq1LeTkmKrwEoKEmg6p9pCH9Nn62nJYAtq6CM/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=ivXvCB8lj4wrNq5SsJ2hH6C1unM%3D)

Kafka Connect 파이프라인 아키텍처

위 사진을 보면 Kafka Connect를 이용해 왼쪽의 DB의 데이터를 **Connect와 Source Connector**를 사용해 Kafka Broker로 보내고 **Connect와 Sink Connector**를 사용해 Kafka에 담긴 데이터를 DB에 저장하는 것을 알 수 있다.

여기서 중요한 건 **Connect와 Connector의 차이**와 **Source Connector**와 **Sink Connector**이다.

> Connect: Connector를 동작하게 하는 프로세서(서버)  
> Connector:  Data Source(DB)의 데이터를 처리하는 소스가 들어있는 jar파일  
>   
> Source Connector: data source에 담긴 데이터를 topic에 담는 역할(Producer)을 하는 connector  
> Sink Connector: topic에 담긴 데이터를 특정 data source로 보내는 역할(Consumer 역할)을 하는 connector

또한 Connect는 단일 모드(Standalone)와 분산 모드(Distributed)로 이루어져있다.

> 단일 모드(Standalone): 하나의 Connect만 사용하는 모드  
>   
> 분산 모드(Distributed): 여러개의 Connect를 한개의 클러스트로 묶어서 사용하는 모드.  
>   -> 특정 Connect가 장애가 발생해도 나머지 Connect가 대신 처리하도록 함

Kafka Connect는 **REST API**를 사용해서 Connector를 등록 및 사용할 수 있다. 이제 예제를 한번 해보자.

---

## Kafka Connect 예제

이번 예제에서 mysql table에 데이터를 insert하면 다른 table에 데이터가 그대로 저장되는 예제를 해본다.

먼저 선작업으로 DB에 Table을 하나 만들자.

```
CREATE SCHEMA test;

CREATE TABLE test.users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
)
```

> Kafka 설치

> 다운로드 링크 : [https://kafka.apache.org/downloads](https://kafka.apache.org/downloads%20)

설치후 해당 디렉토리 이동후 아래처럼 server.properties 파일을 열어

```
$ vi config/server.properties
```

listneres=PLAINTEXT://:9092 주석을 풀고 localhost를 입력해준다.

![](https://blog.kakaocdn.net/dna/y4kFM/btrhTgmlEYP/AAAAAAAAAAAAAAAAAAAAAGRVZ25iBT3jL9LQlpFV_wIMuLU7SzpoOMwd4xp-mOZl/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=3GqRdoNguQJMWxAQoriUduTrP24%3D)

kafka 디렉토리에서 아래의 명령어를 통해 zookeeper server와 kafka broker를 실행해준다.

```
$ ./bin/zookeeper-server-start.sh ./config/zookeeper.properties
$ ./bin/kafka-server-start.sh ./config/server.properties
```

![](https://blog.kakaocdn.net/dna/cJ02bc/btrhTu5K172/AAAAAAAAAAAAAAAAAAAAACbl2pCOIBmZHrXXRZjW0S4UuHQpj-hqYj-Lkez0McDO/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=Vq8Cju6%2F3G2VC9ds4sRiRqkojnY%3D)

zookeeper, kafka 실행

> Kafka Connect 설치

> 다운로드 링크: [https://packages.confluent.io/archieve/6.1/confluent-community-6.1.0.tar.gz](https://packages.confluent.io/archieve/6.1/confluent-community-6.1.0.tar.gz)

설치후 kafka-connect 디렉토리에서 아래 명령어로 kafka-connect 실행(**zookeeper와 kafka broker가 실행되어있어야함**)

```
$ ./bin/connect-distributed ./etc/kafka/connect-distributed.properties
```

실행하고 나서 kafka 디렉토리에서 아래 명령어를 실행하여 topic 리스트를 확인하면

```
$ ./bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

다음과 같은 topic이 생성된 걸 확인할 수 있다.

![](https://blog.kakaocdn.net/dna/Pna2r/btrhTRNeGv9/AAAAAAAAAAAAAAAAAAAAAP-ppGXR-Q0U_OKHBAx0Wqcfrh1aQevE3EuWh69rDyfG/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=cMOnLiXDUYk9AyrCXosYtFLsUsY%3D)

> Connector 설치

> JDBC Connector 다운로드 링크 : [https://docs.confluent.io/5.5.1/connect/kafka-connect-jdbc/index.html](https://docs.confluent.io/5.5.1/connect/kafka-connect-jdbc/index.html)

설치를 하면 아래의 디렉토리가 설치된다.

![](https://blog.kakaocdn.net/dna/bHUlBR/btrhTdQqkwh/AAAAAAAAAAAAAAAAAAAAAAjOKMg0e08K_ZaJbPUpfzFruczBPDsST5X9_50ls9Eu/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=jsmL6GkMQyRZUgewjGOHjcVPU6U%3D)

Kafka Connect 디렉토리에서

```
$ vi etc/kafka/connect-distributed.properties
```

아래처럼 plugin.path={kafka connect jdbc plugin 경로}/lib을 입력해준다.

![](https://blog.kakaocdn.net/dna/4U84s/btrhThlk2uA/AAAAAAAAAAAAAAAAAAAAAIZJq5wJbbgafxaJ2RzOoD6apALr2Xpf8m-8OcrgNVi5/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=rKlhawsAIuO3MWUk4MIkXlAZRPQ%3D)

> Mysql Connector 설치

connector에서 mysql을 사용하기 위해 추가적으로 mysql connector를 설치해줘야한다.

> 다운로드 링크: [https://dev.mysql.com/downloads/connector/j/](https://dev.mysql.com/downloads/connector/j/)  
>   
> 해당 링크로 이동해 설치후 mysql-connector-java-버전.jar파일을  
> **{kafka-connector디렉토리}/share/java/kafka에 복사한다.**

![](https://blog.kakaocdn.net/dna/Qoe4a/btrhS0KyFvI/AAAAAAAAAAAAAAAAAAAAALr42LDXriPTRoWRlpmbm0ekNlNLpgZjZfGajY0V1wZL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=%2B3KRJWNPTWI4txOo5ukhffL%2BbtU%3D)

이제 Connect의 REST API를 통해 Source Connector와 Sink Connector를 생성해보자.

> Source Connector

아래 처럼 connect에 요청을 통해 Source Connector를 생성하자.

```
{
    "name": "my-source-connect",
    "config": {
        "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
        "connection.url": "jdbc:mysql://localhost:3306/test",
        "connection.user":"root",
        "connection.password":"비밀번호",
        "mode":"incrementing",
        "incrementing.column.name" : "id",
        "table.whitelist" : "users",
        "topic.prefix" : "example_topic_",
        "tasks.max" : "1",
    }
}
cUrl -X POST -d @- http://localhost:8083/connectors --header "content-Type:application/json"
```

포스트맨을 사용하면 다음과 같다.

![](https://blog.kakaocdn.net/dna/bkUdSR/btrhWJ8ydGR/AAAAAAAAAAAAAAAAAAAAAKgUPDMLTkCabW3Q7F14GBsxPYuyyNMJzgwUCzoA_LzD/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=SgYPXUbB5Z5%2Fs9Ix4MIHyAJPJ70%3D)

각 속성의 의미는 다음과 같다.

- name : source connector 이름(JdbcSourceConnector를 사용)
- config.connector.class : 커넥터 종류(JdbcSourceConnector 사용)
- config.connection.url : jdbc이므로 DB의 정보 입력
- config.connection.user : DB 유저 정보
- config.connection.password : DB 패스워드
- config.mode : "테이블에 데이터가 추가됐을 때 데이터를 polling 하는 방식"(bulk, incrementing, timestamp, timestamp+incrementing)
- config.incrementing.column.name : incrementing mode일 때 자동 증가 column 이름
- config.table.whitelist : 데이터를 변경을 감지할 table 이름
- config.topic.prefix : kafka 토픽에 저장될 이름 형식 지정 위 같은경우 whitelist를 뒤에 붙여 example_topic_users에 데이터가 들어감
- tasks.max : 커넥터에 대한 작업자 수(본문 인용.. 자세한 설명을 찾지 못함)

실행 후 아래 요청을 통해 생성된 Connectors List를 확인할 수 있다.

```
cUrl -X GET -d @- http://localhost:8083/connectors
```

![](https://blog.kakaocdn.net/dna/bF2AAV/btrhUW8B5DO/AAAAAAAAAAAAAAAAAAAAAK2F4-ula1Tj1NZ5vUuo0j-mhlp_dnnHU-K5OPu6MeAC/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=13bMEAs99X9BhilM8O4IWWmKad4%3D)

이제 test.users table에 데이터를 insert해보자.

![](https://blog.kakaocdn.net/dna/bgzgWh/btrhWI9FEMN/AAAAAAAAAAAAAAAAAAAAAEgqethzdz22h_oPcODdTTejfvA9P4S6e6zMLnMWrk95/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=CYlYNTR5mZbrZA5wcNUhWiBwkjE%3D)

그리고 kafka server 디렉토리에서 아래 명령어를 통해 topic 리스트를 확인해보면 example_topic_users 토픽이 생성된 것을 확인할 수 있다.(DB에 데이터를 삽입함으로써 Source Connector가 DB데이터를 topic에 push한 것)

```
$ ./bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

![](https://blog.kakaocdn.net/dna/eda1IQ/btrhUku9pIc/AAAAAAAAAAAAAAAAAAAAAA3GrR7PDxQQ3Gwct7VzHzFfmduuEGmr-rbjMBhyb9Iq/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=cOeBbbATRFlslVb2Xw39tP0rRro%3D)

토픽 리스트

> Sink Connector

이제 Source Connector를 통해 topic에 넣은 데이터를 Sink하기 위해 Sink Connector를 생성해보자

Source Connector와 동일하게 아래처럼 API통해 생성한다.

```
{
    "name": "my-pksink-connect",
    "config": {
        "connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",
        "connection.url": "jdbc:mysql://localhost:3306/test",
        "connection.user":"root",
        "connection.password":"비밀번호",
        "auto.create":"true",
        "auto.evolve":"true",
        "delete.enabled":"false",
        "tasks.max":"1",
        "topics":"example_topic_users"
    }
}

cUrl -X POST -d @- http://localhost:8083/connectors --header "content-Type:application/json"
```

![](https://blog.kakaocdn.net/dna/bkyuud/btrhUlHBfWd/AAAAAAAAAAAAAAAAAAAAAM9UsDzHUV_iEq1mfZ6a0ZNHCwKbe6J81MMSH5VlGQOm/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=k0fsJjJBIFnWhNR2GBYaG6sCu8A%3D)

sourceConnector와 겹치는 속성을 제외한 속성은 다음과 같은 뜻을 가진다

- auto.create : 데이터를 넣을 테이블이 누락되었을 경우 자동 테이블 생성 여부
- auto.evolve : 특정 데이터의 열이 누락된 경우 대상 테이블에 ALTER 구문을 날려 자동으로 테이블 구조를 바꾸는지 여부 (하지만 데이터 타입 변경, 컬럼 제거, 키본 키 제약 조건 추가등은 시도되지 않는다)
- delete.enabled : 삭제 모드 여부

더 자세한 속성은 해당 링크에서 확인할 수 있다.

[https://docs.confluent.io/kafka-connect-jdbc/current/sink-connector/sink_config_options.html](https://docs.confluent.io/kafka-connect-jdbc/current/sink-connector/sink_config_options.html)

생성후 users table에 데이터를 insert하면

![](https://blog.kakaocdn.net/dna/b5aURm/btrh0ErTV6M/AAAAAAAAAAAAAAAAAAAAAH1gKmi_ZW0BM5Q9KhyEVKIhuL7UL9sk-_7XnjcaB3qs/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=ANNKZEqjI1at6P32KMUJSpVMEJc%3D)

example_topic_users table이 생성된 것을 볼 수 있고

![](https://blog.kakaocdn.net/dna/DsHyb/btrhX1gQbYK/AAAAAAAAAAAAAAAAAAAAAA1P_biHpl7YdsaGUdNIMxQ_0LSTNRZnwnNOBecKGaB0/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=PLKdev9khGEn6ZsbM7658HU8k%2Fo%3D)![](https://blog.kakaocdn.net/dna/1erp9/btrhTSeCnhT/AAAAAAAAAAAAAAAAAAAAAMV5QW0LslbB5zqSWXhymWp_7kenv-MPLSJGr5QZJUOS/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1788188399&allow_ip=&allow_referer=&signature=vDKVOZdG%2BjIfXPqRqxk8JdJ31J0%3D)

users table과 example_topic_users table

서로 테이블의 내용이 같은 것을 확인할 수 있다.
