---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - kraft
  - metadata
sources:
  - raw/articles/KRaft vs ZooKeeper.md
---

# KRaft

KRaft는 Kafka가 ZooKeeper 없이 metadata를 관리하기 위한 mode다. controller quorum이 metadata 관련 요청과 cluster coordination을 담당하고, Raft 기반으로 controller 간 상태를 조정한다.

## 핵심 포인트

- [[ZooKeeper]] 의존성을 제거한다.
- broker와 controller 역할을 분리한다.
- 서버 식별에는 `node.id`가 중요하다.
- cluster feature level은 `metadata.version`으로 관리한다.
- controller 관련 명령은 `--bootstrap-controller`를 사용하는 경우가 있다.

## 운영 영향

- ZooKeeper 전용 설정과 metric은 제거되거나 대체된다.
- 일부 설정은 dynamic update 대신 restart가 필요할 수 있다.
- topic/config policy class는 broker가 아니라 controller에 배포해야 할 수 있다.
- custom principal 구현은 controller forwarding을 위해 serialization 요구사항을 만족해야 한다.

## 연결 문서

- [[Kafka]]
- [[ZooKeeper]]
- [[Kafka 브로커]]
- [[2026-08-21-kafka-kraft-vs-zookeeper|Kafka KRaft vs ZooKeeper]]

## Open Questions

- 현재 운영 대상 Kafka cluster는 KRaft mode인가?
- controller와 broker를 같은 node에 둘 것인가, 분리할 것인가?
- KRaft 전환 시 metric, 설정, 배포 manifest 중 무엇이 깨질 수 있는가?
