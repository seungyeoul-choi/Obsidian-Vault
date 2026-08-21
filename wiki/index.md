---
type: index
status: seed
created: 2026-08-21
updated: 2026-08-21
tags:
  - llm-wiki
---

# Index

이 파일은 위키 전체의 콘텐츠 카탈로그다. Codex는 질문 답변, ingest, lint 작업을 시작할 때 이 파일을 먼저 읽는다.

## Entry Points

- [[Home MOC]]: 전체 내비게이션
- [[overview]]: 위키의 현재 큰 그림
- [[Backend MOC]]: 백엔드 학습과 실무 지식
- [[Data MOC]]: 데이터 저장소와 검색
- [[Infra MOC]]: 클라우드, 컨테이너, 운영
- [[Projects MOC]]: 업무 프로젝트
- [[Learning MOC]]: 학습 로드맵과 기록

## Sources

- [[2026-08-21-kafka-introduction|Kafka Introduction]]: Kafka와 event streaming의 기본 개념
- [[2026-08-21-kafka-design|Kafka Design]]: Kafka 저장소, batching, consumer, replication, compaction 설계
- [[2026-08-21-kafka-kraft-vs-zookeeper|Kafka KRaft vs ZooKeeper]]: KRaft mode와 ZooKeeper mode의 운영 차이

## Concepts

- [[주문 처리 시스템]]: 현재 업무 맥락의 중심 시스템
- [[성능 최적화]]: 처리량, 지연시간, 병목 분석
- [[Spring Boot]]: 백엔드 애플리케이션 프레임워크
- [[MySQL]]: 주문 데이터 저장소 후보 지식 축
- [[Redis]]: 캐시, 분산 락, 임시 상태 저장
- [[Kafka]]: 이벤트 기반 처리와 비동기 메시징 플랫폼
- [[Kafka 브로커]]: topic partition log를 저장하고 producer/consumer 요청을 처리하는 서버
- [[Kafka 토픽과 파티션]]: Kafka의 저장, 순서 보장, 병렬 처리 단위
- [[Kafka Consumer Group]]: partition을 여러 consumer가 나누어 처리하는 확장 모델
- [[Kafka 전달 보장]]: at-most-once, at-least-once, exactly-once 설계 기준
- [[KRaft]]: ZooKeeper 없는 Kafka metadata 관리 mode
- [[ZooKeeper]]: 과거 Kafka cluster coordination에 쓰인 외부 시스템
- [[Kubernetes]]: 컨테이너 오케스트레이션과 배포 운영

## Projects

- [[주문 처리 시스템 최적화]]: 현재 진행 중인 프로젝트

## Questions

아직 파일로 남긴 질문 답변이 없다.

## Syntheses

아직 종합 분석 문서가 없다.

## Maintenance Notes

- 새 문서를 만들면 이 index와 관련 MOC에 링크를 추가한다.
- 자료별 요약은 `wiki/sources/`에 둔다.
- 재사용 가능한 질문 답변은 `wiki/questions/`에 둔다.
