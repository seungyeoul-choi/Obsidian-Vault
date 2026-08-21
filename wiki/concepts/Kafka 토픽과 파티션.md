---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - topic
  - partition
sources:
  - raw/articles/Introduction.md
  - raw/articles/Design.md
---

# Kafka 토픽과 파티션

Kafka topic은 event를 저장하는 논리적 단위이고, partition은 topic을 여러 broker에 분산 배치하기 위한 log 단위다.

## 핵심 규칙

- topic은 여러 producer와 여러 consumer가 동시에 사용할 수 있다.
- partition은 append-only log처럼 동작한다.
- 같은 event key를 가진 record는 같은 partition에 들어가도록 설계할 수 있다.
- Kafka는 같은 topic-partition 안에서 record 순서를 보장한다.
- partition은 replication factor에 따라 여러 broker에 복제된다.

## 주문 처리 시스템 적용

- 주문 ID를 key로 쓰면 같은 주문의 이벤트 순서를 유지하기 쉽다.
- 사용자 ID나 상품 ID를 key로 쓰면 사용자별/상품별 locality가 생기지만 hot key 위험이 달라진다.
- partition 수는 처리량 확장 단위이지만, 순서 보장 범위와 consumer parallelism에도 영향을 준다.

## 연결 문서

- [[Kafka]]
- [[Kafka 브로커]]
- [[Kafka Consumer Group]]
- [[주문 처리 시스템]]
- [[주문 처리 시스템 최적화]]

## Open Questions

- 주문 이벤트 topic의 partition key는 무엇이어야 하는가?
- 순서 보장이 필요한 이벤트와 병렬성이 더 중요한 이벤트를 topic으로 분리해야 하는가?
