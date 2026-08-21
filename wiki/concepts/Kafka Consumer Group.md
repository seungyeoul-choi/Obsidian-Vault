---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - consumer
  - consumer-group
sources:
  - raw/articles/Design.md
---

# Kafka Consumer Group

Kafka consumer group은 여러 consumer가 topic partition을 나누어 처리하게 만드는 확장 단위다.

## 핵심 포인트

- 일반 consumer group에서는 한 partition이 같은 group 내의 한 consumer에게 할당된다.
- consumer의 처리 위치는 partition별 offset으로 표현된다.
- offset을 언제 저장하느냐에 따라 at-most-once, at-least-once 처리 특성이 달라진다.
- static membership은 restart나 배포 시 불필요한 rebalance를 줄여 stream application 가용성을 높인다.

## Share Consumer

Kafka에는 traditional consumer group과 다른 share group/consumer 개념도 있다. share group은 partition보다 많은 consumer가 협력적으로 record를 처리할 수 있고, record별 acknowledge와 delivery attempt 관리에 초점을 둔다.

## 주문 처리 시스템 적용

- 주문 처리 consumer는 partition 수와 consumer 수의 관계를 고려해야 한다.
- 배포나 restart 때 rebalance로 처리가 멈추거나 state 복구가 오래 걸리는지 관찰해야 한다.
- 중복 처리가 가능한 idempotent consumer인지가 at-least-once 운영의 핵심이다.

## 연결 문서

- [[Kafka]]
- [[Kafka 토픽과 파티션]]
- [[Kafka 전달 보장]]
- [[주문 처리 시스템 최적화]]
