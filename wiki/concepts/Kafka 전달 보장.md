---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - delivery-semantics
  - reliability
sources:
  - raw/articles/Design.md
---

# Kafka 전달 보장

Kafka 전달 보장은 producer durability, broker replication, consumer offset commit, idempotent producer, transaction 설정이 함께 결정한다.

## 기본 의미

- at-most-once: 메시지가 유실될 수 있지만 중복 처리되지 않는다.
- at-least-once: 메시지는 유실되지 않지만 중복 처리될 수 있다.
- exactly-once: Kafka topic에서 읽고 처리한 뒤 Kafka topic에 쓰는 흐름에서 transaction과 offset commit을 함께 다룰 때 구현할 수 있다.

## Producer 측면

- producer가 commit acknowledgement를 기다릴수록 durability는 높아지고 latency는 늘 수 있다.
- idempotent producer는 retry로 인한 중복 append를 줄인다.
- transaction은 여러 topic partition에 대한 write와 consumer offset update를 원자적으로 묶을 수 있다.

## Consumer 측면

- 처리 전에 offset을 저장하면 crash 시 처리 누락 위험이 있다.
- 처리 후 offset을 저장하면 crash 시 중복 처리 위험이 있다.
- 외부 DB에 결과를 쓰는 경우, offset과 output 저장의 원자성을 별도로 설계해야 한다.

## 주문 처리 시스템 적용

- 결제, 재고, 주문 상태 변경 consumer는 중복 이벤트를 견딜 수 있도록 멱등성을 먼저 설계해야 한다.
- exactly-once가 필요한지 판단할 때는 Kafka 내부 처리인지 외부 시스템 쓰기가 포함되는지 구분해야 한다.
- read committed isolation과 transaction 설정은 복잡도를 동반하므로 명확한 비즈니스 필요가 있을 때 적용한다.

## 연결 문서

- [[Kafka]]
- [[Kafka Consumer Group]]
- [[주문 처리 시스템]]
- [[주문 처리 시스템 최적화]]
