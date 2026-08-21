---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - zookeeper
  - metadata
sources:
  - raw/articles/KRaft vs ZooKeeper.md
---

# ZooKeeper

ZooKeeper는 과거 Kafka cluster metadata와 coordination에 사용되던 외부 시스템이다. 최신 Kafka 운영에서는 [[KRaft]] mode가 ZooKeeper 의존성을 제거하는 방향으로 중요해졌다.

## Kafka 맥락

- ZooKeeper mode에서는 broker 등록, cluster metadata, 여러 dynamic configuration과 metric이 ZooKeeper에 의존했다.
- KRaft mode에서는 ZooKeeper 관련 설정과 metric 다수가 제거된다.
- ZooKeeper mode에서 KRaft mode로 이전할 때는 설정, metric, policy class 배포 위치를 점검해야 한다.

## 연결 문서

- [[Kafka]]
- [[KRaft]]
- [[2026-08-21-kafka-kraft-vs-zookeeper|Kafka KRaft vs ZooKeeper]]

## Open Questions

- 기존 시스템 문서나 대시보드가 ZooKeeper mode를 전제로 작성되어 있는가?
- Kafka version upgrade 계획에 ZooKeeper 제거가 포함되어 있는가?
