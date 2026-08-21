---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - messaging
sources:
  - raw/articles/Introduction.md
  - raw/articles/Design.md
  - raw/articles/KRaft vs ZooKeeper.md
---

# Kafka

Kafka는 주문 처리 시스템에서 이벤트 기반 비동기 처리, 시스템 간 결합도 완화, 처리량 확장에 사용할 수 있는 메시징 플랫폼이다.

## 핵심 모델

- Kafka는 event streaming platform이다. 이벤트를 publish/subscribe하고, durable log로 보관하고, 실시간 또는 사후에 처리한다.
- producer와 consumer는 서로 분리되어 있어 producer가 consumer 처리 속도에 직접 묶이지 않는다.
- 이벤트는 key, value, timestamp, headers를 가진 record/message로 다룬다.
- 이벤트는 [[Kafka 토픽과 파티션]]에 저장된다. partition은 확장성과 순서 보장의 기본 단위다.
- [[Kafka 브로커]]는 partition log를 저장하고 producer/consumer 요청을 처리한다.

## 설계 관점

- Kafka는 전통적인 메시지 큐보다 append-only database log에 가깝다.
- filesystem, OS page cache, sequential I/O, batching, zero-copy를 활용해 high throughput을 만든다.
- consumer는 broker가 push하는 방식이 아니라 fetch 기반 pull 모델을 사용한다.
- consumer position은 partition별 offset으로 표현되어 재처리와 장애 복구가 가능하다.
- replication은 partition leader/follower와 ISR을 중심으로 동작한다.

## 운영 관점

- [[KRaft]] mode에서는 [[ZooKeeper]] 의존성이 제거되고 controller quorum이 metadata를 관리한다.
- broker, partition, ISR, consumer lag, quota, log retention/compaction을 함께 모니터링해야 한다.
- multi-tenant cluster에서는 client quota가 장애 전파를 줄이는 보호 장치가 된다.

## 주문 처리 시스템 적용

- 주문 ID를 event key로 사용하면 같은 주문의 이벤트 순서를 유지하기 쉽다.
- 결제, 재고, 배송 같은 하위 시스템을 event로 분리하면 결합도를 낮출 수 있다.
- [[Kafka 전달 보장]]은 business idempotency와 함께 설계해야 한다.
- 주문 상태 projection이나 캐시 재구축에는 log compaction 기반 changelog topic을 검토할 수 있다.

## 연결 문서

- [[Data MOC]]
- [[주문 처리 시스템]]
- [[성능 최적화]]
- [[주문 처리 시스템 최적화]]
- [[2026-08-21-kafka-introduction|Kafka Introduction]]
- [[2026-08-21-kafka-design|Kafka Design]]
- [[2026-08-21-kafka-kraft-vs-zookeeper|Kafka KRaft vs ZooKeeper]]
- [[Kafka 브로커]]
- [[Kafka 토픽과 파티션]]
- [[Kafka Consumer Group]]
- [[Kafka 전달 보장]]
- [[KRaft]]
- [[ZooKeeper]]

## Open Questions

- 현재 주문 처리 시스템에서 Kafka는 event log, work queue, changelog 중 어떤 역할을 하는가?
- topic partition key는 주문 ID, 사용자 ID, 상품 ID 중 무엇이 적합한가?
- consumer는 at-least-once와 멱등성으로 충분한가, Kafka transaction이 필요한가?
- 운영 cluster는 KRaft mode인가 ZooKeeper mode인가?
