---
type: source
status: ingested
created: 2026-08-21
updated: 2026-08-21
tags:
  - kafka
  - kraft
  - zookeeper
  - source
aliases:
  - Kafka KRaft vs ZooKeeper
source_path: raw/articles/KRaft vs ZooKeeper.md
source_url: https://kafka.apache.org/43/getting-started/zk2kraft/
---

# Kafka KRaft vs ZooKeeper

## Summary

Apache Kafka 공식 KRaft vs ZooKeeper 문서는 [[KRaft]] mode와 [[ZooKeeper]] mode의 운영 차이를 정리한다. KRaft에서는 Kafka가 ZooKeeper 의존성을 제거하고 metadata 관리를 Kafka 내부 controller quorum으로 처리한다. 그 결과 여러 ZooKeeper 전용 설정, metric, 동작 방식이 사라지거나 KRaft 방식으로 대체된다.

## Key Claims

- KRaft mode에서는 ZooKeeper 관련 설정이 제거된다. 예: `zookeeper.connect`, ZooKeeper SSL 설정, ZooKeeper session/connection 설정.
- KRaft mode는 broker와 controller 역할을 명확히 분리한다. broker는 data request를, controller는 metadata request를 관리한다.
- controller 간 조정은 Raft 기반 quorum controller가 담당한다.
- KRaft mode에서는 `broker.id`보다 `node.id`가 서버 식별자로 중요하다.
- ZooKeeper mode의 `inter.broker.protocol.version` 대신 KRaft에서는 `metadata.version`으로 cluster feature level을 관리한다.
- 일부 ZooKeeper 기반 dynamic configuration은 KRaft에서 broker/controller restart가 필요할 수 있다.
- controller log level 변경에는 `--bootstrap-controller`를 사용한다.
- KRaft mode에서는 ZooKeeper 관련 metric이 제거되고, network processor와 expired connection 관련 metric은 KRaft에 맞는 metric으로 대체된다.
- CreateTopicPolicy와 AlterConfigPolicy 같은 policy class는 KRaft에서 broker가 아니라 controller에 배포해야 한다.
- custom KafkaPrincipalBuilder는 KRaft에서 KafkaPrincipalSerde도 구현해야 broker가 controller로 request를 forward할 수 있다.

## Practical Notes

- 신규 Kafka 운영을 검토할 때는 ZooKeeper 기반 운영 지식과 KRaft 운영 지식을 구분해야 한다.
- 기존 ZooKeeper mode cluster를 이전한다면 설정 제거, metric 대체, policy jar 배포 위치, principal serialization 요구사항을 별도 체크리스트로 관리해야 한다.
- 모니터링 대시보드는 ZooKeeper metric 이름에 의존하지 않도록 KRaft metric으로 갱신해야 한다.
- 주문 처리 시스템의 Kafka 운영 문서에는 Kafka version과 cluster mode를 명시해야 한다.

## Links To Update

- [[Kafka]]
- [[KRaft]]
- [[ZooKeeper]]
- [[Kafka 브로커]]
- [[Kubernetes]]
- [[주문 처리 시스템 최적화]]

## Open Questions

- 현재 또는 목표 Kafka cluster는 KRaft mode인가 ZooKeeper mode인가?
- 운영 대시보드와 alert rule이 ZooKeeper 전용 metric에 의존하고 있는가?
- controller/broker 역할 분리 배포가 Kubernetes manifest에 어떻게 표현되어 있는가?
