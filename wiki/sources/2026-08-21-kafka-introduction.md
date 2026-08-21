---
type: source
status: ingested
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - event-streaming
  - source
aliases:
  - Kafka Introduction
source_path: raw/articles/Introduction.md
source_url: https://kafka.apache.org/43/getting-started/introduction/
---

# Kafka Introduction

## Summary

Apache Kafka 공식 Introduction 문서는 [[Kafka]]를 이벤트 스트리밍 플랫폼으로 소개한다. 이벤트 스트리밍은 여러 시스템에서 발생하는 이벤트를 실시간으로 수집, 저장, 처리, 반응, 라우팅하는 방식이다. Kafka는 이 흐름을 end-to-end로 다루기 위해 publish/subscribe, durable event storage, stream processing 세 가지 능력을 하나의 분산 플랫폼으로 제공한다.

## Key Claims

- Kafka는 이벤트를 실시간으로 캡처하고, 오래 보관하고, 필요할 때 재처리할 수 있는 플랫폼이다.
- Kafka 클러스터는 서버와 클라이언트로 구성된다. 서버에는 데이터를 저장하는 [[Kafka 브로커]]와 외부 시스템 연동을 담당하는 Kafka Connect 프로세스가 포함될 수 있다.
- 이벤트는 key, value, timestamp, headers를 가진 record/message로 취급된다.
- producer와 consumer는 서로 분리되어 있어, producer가 consumer를 기다리지 않아도 된다.
- 이벤트는 [[Kafka 토픽과 파티션]]에 저장되며, 같은 key를 가진 이벤트는 같은 partition으로 들어가 순서 보장을 받을 수 있다.
- topic partition은 replication factor를 통해 여러 broker에 복제될 수 있다.
- Java/Scala 기준으로 Admin API, Producer API, Consumer API, Kafka Streams API, Kafka Connect API가 핵심 API다.

## Practical Notes

- 주문 처리 시스템에서 Kafka를 도입할 때는 주문 생성, 결제, 재고 차감, 배송 상태 변경 같은 사건을 event로 모델링할 수 있다.
- 주문 ID 또는 사용자 ID를 event key로 쓰면 같은 key의 이벤트가 같은 partition에 들어가므로 순서가 필요한 처리에 유리하다.
- Kafka는 메시지를 소비했다고 바로 삭제하지 않는다. retention 설정에 따라 보관되므로 재처리, 장애 복구, 신규 consumer 추가에 유리하다.
- replication factor 3은 운영 환경에서 흔한 기본값으로 소개된다.

## Links To Update

- [[Kafka]]
- [[Kafka 브로커]]
- [[Kafka 토픽과 파티션]]
- [[주문 처리 시스템]]
- [[주문 처리 시스템 최적화]]

## Open Questions

- 주문 처리 시스템에서 event key는 주문 ID, 사용자 ID, 상품 ID 중 무엇이어야 하는가?
- 어떤 이벤트는 실시간 처리만 필요하고, 어떤 이벤트는 장기 보관과 재처리가 필요한가?
- Kafka Connect를 통해 MySQL 변경 이벤트를 직접 가져올 필요가 있는가?
