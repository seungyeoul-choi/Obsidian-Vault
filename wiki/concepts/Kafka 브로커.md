---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - broker
sources:
  - raw/articles/Introduction.md
  - raw/articles/Design.md
  - raw/articles/KRaft vs ZooKeeper.md
---

# Kafka 브로커

Kafka 브로커는 topic partition의 log를 저장하고 producer/consumer 요청을 처리하는 Kafka server다.

## 역할

- producer가 보낸 record를 partition leader log에 append한다.
- consumer fetch 요청에 따라 log segment를 반환한다.
- topic partition의 leader 또는 follower가 될 수 있다.
- follower는 leader의 log를 복제해 [[Kafka]]의 fault tolerance를 만든다.

## 설계 포인트

- Kafka는 filesystem과 OS page cache를 적극 활용한다.
- log는 append 중심 구조라 순차 I/O와 batching에 유리하다.
- broker는 message set, zero-copy, compression을 통해 high throughput을 만든다.
- replication에서는 partition별 leader와 follower, ISR 상태가 중요하다.

## KRaft 맥락

[[KRaft]] mode에서는 broker와 controller 역할이 분리된다. broker는 data request를 처리하고, controller는 metadata request를 관리한다.

## 연결 문서

- [[Kafka]]
- [[Kafka 토픽과 파티션]]
- [[KRaft]]
- [[2026-08-21-kafka-design|Kafka Design]]
- [[2026-08-21-kafka-introduction|Kafka Introduction]]
