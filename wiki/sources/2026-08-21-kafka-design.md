---
type: source
status: ingested
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - architecture
  - source
aliases:
  - Kafka Design
source_path: raw/articles/Design.md
source_url: https://kafka.apache.org/43/design/design/
---

# Kafka Design

## Summary

Kafka 공식 Design 문서는 Kafka가 대용량 실시간 데이터 피드를 처리하기 위해 어떤 저장소, 네트워크, producer/consumer, replication, retention 설계를 선택했는지 설명한다. Kafka의 핵심 설계는 전통적인 메시지 큐보다 데이터베이스 로그에 가깝다. 순차 I/O, OS page cache, batching, zero-copy, partitioned log, consumer offset, ISR 기반 replication이 처리량과 복구성을 만든다.

## Key Claims

- Kafka는 고처리량, 대규모 backlog, 낮은 지연시간, 분산 실시간 처리, 장애 허용을 동시에 목표로 설계됐다.
- persistence는 heap cache보다 filesystem과 OS page cache를 활용하는 방향으로 설계됐다.
- Kafka log는 append 중심 구조이며, 일반적인 tree 기반 random access 구조보다 대용량 순차 처리에 유리하다.
- batching은 네트워크 왕복, 디스크 I/O, 메모리 복사를 줄이는 핵심 최적화다.
- producer는 partition leader broker로 직접 쓰며, key 기반 partitioning을 통해 locality와 순서를 만들 수 있다.
- consumer는 broker가 push하는 방식이 아니라 fetch 기반 pull 모델을 사용한다.
- consumer position은 partition별 offset으로 표현되므로 ack 상태를 메시지별로 broker가 복잡하게 관리하는 방식보다 단순하다.
- Kafka의 delivery semantics는 producer durability, consumer offset commit, idempotent producer, transaction, isolation level 조합으로 결정된다.
- replication 단위는 topic partition이며, leader와 follower, ISR을 통해 failover와 committed message 보장을 제공한다.
- log compaction은 key별 최신 상태를 보존하여 캐시 재구축, changelog, event sourcing, stream processing state 복구에 유용하다.
- quota는 multi-tenant Kafka cluster에서 producer/consumer가 broker 자원을 독점하지 않도록 보호한다.

## Practical Notes

- 주문 처리 시스템에서 Kafka 성능을 볼 때는 단순히 broker 수보다 partition 수, key 분포, batch 크기, consumer lag, ISR 상태를 함께 봐야 한다.
- 처리량을 높이려면 작은 메시지를 즉시 보내는 방식보다 batch와 compression을 고려해야 한다.
- 순서 보장이 필요한 주문 흐름은 같은 key가 같은 partition에 들어가도록 설계해야 한다.
- at-least-once가 기본에 가깝고, exactly-once는 Kafka 내부 read-process-write 흐름에서 transaction과 read committed 설정을 제대로 써야 의미가 있다.
- 외부 DB에 쓰는 consumer는 offset 저장 위치와 출력 저장소의 원자성을 별도로 설계해야 한다.
- log compaction은 주문의 최신 상태 projection이나 캐시 재구축용 changelog topic에 적합할 수 있다.
- quota는 사내 여러 서비스가 같은 Kafka cluster를 공유할 때 장애 전파를 줄이는 운영 장치다.

## Links To Update

- [[Kafka]]
- [[Kafka 브로커]]
- [[Kafka 토픽과 파티션]]
- [[Kafka Consumer Group]]
- [[Kafka 전달 보장]]
- [[성능 최적화]]
- [[주문 처리 시스템 최적화]]

## Open Questions

- 현재 주문 처리 시스템의 Kafka topic은 event log 성격인가, 상태 changelog 성격인가?
- 주문 이벤트 consumer는 at-least-once와 idempotency만으로 충분한가, transaction 기반 exactly-once가 필요한가?
- 특정 주문 key에 트래픽이 몰릴 때 partitioning 전략은 어떻게 보완할 것인가?
- 운영 cluster에서 quota를 사용자, client-id, 서비스 단위 중 무엇으로 관리해야 하는가?
