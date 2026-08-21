---
type: log
status: active
created: 2026-08-21
updated: 2026-08-21
tags:
  - llm-wiki
  - log
---

# Log

이 파일은 append-only 변경 이력이다. 기존 항목은 수정하지 않고, 정정이 필요하면 새 항목을 추가한다.

## [2026-08-21] setup | LLM Wiki 초기 구조 생성

- `raw/` 원천 자료 계층을 만들었다.
- `wiki/` LLM 생성 지식 계층을 만들었다.
- `index.md`, `overview.md`, MOC, 초기 개념/프로젝트 문서를 만들었다.
- `AGENTS.md`에 ingest, query, lint, MOC 운영 규칙을 추가했다.

## [2026-08-21] ingest | Kafka 공식 문서 3개

- 처리한 원천 자료:
  - `raw/articles/Introduction.md`
  - `raw/articles/Design.md`
  - `raw/articles/KRaft vs ZooKeeper.md`
- 생성한 source 요약:
  - [[2026-08-21-kafka-introduction|Kafka Introduction]]
  - [[2026-08-21-kafka-design|Kafka Design]]
  - [[2026-08-21-kafka-kraft-vs-zookeeper|Kafka KRaft vs ZooKeeper]]
- 생성/갱신한 주요 개념:
  - [[Kafka]]
  - [[Kafka 브로커]]
  - [[Kafka 토픽과 파티션]]
  - [[Kafka Consumer Group]]
  - [[Kafka 전달 보장]]
  - [[KRaft]]
  - [[ZooKeeper]]
- 갱신한 내비게이션:
  - [[Data MOC]]
  - [[Home MOC]]
  - [[주문 처리 시스템 최적화]]
  - [[index]]
