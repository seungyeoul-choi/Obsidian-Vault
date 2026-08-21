# Vault Schema

이 파일은 옵시디언 볼트 전용 스키마입니다. Codex가 볼트 작업을 할 때 이 규칙을 따릅니다.

## 사용자 컨텍스트

- 이름: 김로이
- 역할: 주문시스템을 개발하는 3년차 개발자
- 백엔드: Java, Spring Boot, Python
- 데이터: MySQL, Redis, ElasticSearch
- 메시징: Kafka
- 인프라: AWS, Docker, K8S
- 일상 업무: 하루 1시간 기술 공부, 1~2시간 미팅, 4시간 정도 개발 업무
- 현재 프로젝트: 주문 처리 시스템 최적화

## 운영 모델

이 볼트는 LLM Wiki 방식으로 운영한다. 사용자는 원천 자료를 큐레이션하고 질문과 방향을 제시한다. Codex는 원천 자료를 읽고, 요약하고, 연결하고, 갱신하며, 위키의 일관성을 유지한다.

볼트는 세 계층으로 나눈다.

1. `raw/`: 원천 자료 보관소. 기사, 논문, 책 챕터, 회의 메모, 로그, 이미지, 데이터 파일을 둔다. Codex는 읽을 수 있지만 원문을 수정하지 않는다.
2. `wiki/`: LLM이 생성하고 유지하는 지식 위키. 요약, 개념, 엔티티, 프로젝트 문서, 질문 답변, 종합 분석을 둔다.
3. `AGENTS.md`: 위키 운영 스키마. 디렉터리 구조, 문서 규칙, ingest/query/lint 절차를 정의한다.

## 디렉터리 규칙

- `raw/inbox/`: 새로 수집한 자료를 임시로 넣는 곳.
- `raw/articles/`: 웹 클리핑, 블로그, 문서화된 글.
- `raw/books/`: 책, 챕터, 독서 노트 원문.
- `raw/papers/`: 논문, 리포트, 백서.
- `raw/meetings/`: 회의록, 인터뷰, 대화 기록.
- `raw/assets/`: 이미지와 첨부파일. Obsidian 첨부 폴더로 사용할 수 있다.
- `wiki/index.md`: 위키 전체 카탈로그. Codex는 작업할 때 먼저 이 파일을 읽는다.
- `wiki/log.md`: append-only 변경 이력. ingest, query, lint, maintenance를 시간순으로 기록한다.
- `wiki/overview.md`: 현재 위키의 큰 그림과 주요 축.
- `wiki/moc/`: Map Of Content. 사람이 탐색하기 좋은 내비게이션 문서.
- `wiki/sources/`: 원천 자료별 LLM 요약.
- `wiki/concepts/`: 개념, 패턴, 기술 주제.
- `wiki/entities/`: 사람, 조직, 서비스, 제품, 시스템 구성요소.
- `wiki/projects/`: 프로젝트와 업무 맥락.
- `wiki/questions/`: 질문과 답변 중 다시 볼 가치가 있는 탐색 결과.
- `wiki/syntheses/`: 여러 자료를 종합한 분석, 비교, 의사결정 기록.
- `wiki/templates/`: 새 문서를 만들 때 사용할 템플릿.

## 문서 작성 규칙

- 모든 위키 문서는 Markdown으로 작성한다.
- Obsidian 링크는 `[[문서명]]` 형식을 우선 사용한다.
- 파일명은 사람이 읽기 쉬운 한국어 또는 기술 고유명사를 사용한다.
- 원천 자료 요약은 `wiki/sources/YYYY-MM-DD-자료명.md` 형식을 권장한다.
- 질문 결과는 `wiki/questions/YYYY-MM-DD-질문요약.md` 형식을 권장한다.
- 새 문서에는 가능한 한 YAML frontmatter를 둔다.

권장 frontmatter:

```yaml
---
type: concept
status: seed
created: 2026-08-21
updated: 2026-08-21
tags: []
sources: []
---
```

`type` 값은 `moc`, `source`, `concept`, `entity`, `project`, `question`, `synthesis`, `index`, `log`, `overview` 중 하나를 우선 사용한다.

## Raw frontmatter 규칙

`raw/` 원천 자료의 본문은 수정하지 않는다. 다만 분류와 ingest 상태 추적을 위해 YAML frontmatter 메타데이터만 갱신할 수 있다.

새 raw 문서에는 가능한 한 다음 필드를 둔다.

```yaml
tags: []
tagged: false
tagged_at:
ingested: false
ingested_at:
ingest_refs: []
```

- `tags`는 현재 볼트에서 관리 중인 태그와 wiki 개념을 우선 참고해 붙인다.
- `tagged`는 Obsidian Properties에서 체크박스처럼 다룰 수 있는 boolean 값이다.
- 태그 작업이 끝나면 `tagged: true`로 바꾸고 `tagged_at: YYYY-MM-DD`를 기록한다.
- `ingested`는 Obsidian Properties에서 체크박스처럼 다룰 수 있는 boolean 값이다.
- ingest 전에는 `ingested: false`로 둔다.
- ingest가 끝나면 `ingested: true`로 바꾸고 `ingested_at: YYYY-MM-DD`를 기록한다.
- `ingest_refs`에는 생성된 `wiki/sources/` 요약 문서 링크를 넣는다.
- raw 본문, 원문 제목, 원문 URL, clipping 내용은 태그 또는 ingest 상태 표시를 위해 수정하지 않는다.
- 태그 작업은 ingest와 별개다. `ingested: true` 파일은 ingest 대상에서는 제외하지만, 태그 정리 대상에서는 제외하지 않는다.
- 새 태그는 남발하지 않는다. 기존 tags, `wiki/index.md`, `wiki/moc/`, `wiki/concepts/`를 우선 참고하되, 기존 태그로 설명되지 않는 명확한 개념이면 새 태그를 추가할 수 있다. 새로 추가한 태그는 작업 보고에 명시한다.

## MOC 운영 규칙

MOC는 내용을 길게 설명하는 문서가 아니라 탐색을 돕는 지도다.

- `wiki/moc/Home MOC.md`를 최상위 내비게이션으로 유지한다.
- 주요 업무/학습 축마다 MOC를 만든다.
- 새 개념, 프로젝트, 질문 문서가 생기면 관련 MOC에 링크를 추가한다.
- MOC에는 핵심 링크, 최근 업데이트, 다음에 볼 질문을 짧게 둔다.
- 문서가 많아져도 MOC는 스캔 가능해야 하므로 긴 설명은 개별 문서로 분리한다.

초기 MOC:

- `[[Home MOC]]`: 전체 진입점
- `[[Backend MOC]]`: Java, Spring Boot, Python, API, 성능
- `[[Data MOC]]`: MySQL, Redis, ElasticSearch, 데이터 모델링
- `[[Infra MOC]]`: AWS, Docker, Kubernetes, 운영
- `[[Projects MOC]]`: 주문 처리 시스템 최적화와 업무 프로젝트
- `[[Learning MOC]]`: 하루 1시간 기술 학습 기록과 로드맵

## Ingest 절차

사용자가 새 자료 처리를 요청하면 Codex는 다음 순서로 작업한다.

이 볼트에서 ingest 작업을 수행할 때는 프로젝트 로컬 스킬 `.codex/skills/ingest/SKILL.md`를 우선 적용한다.

1. `wiki/index.md`와 관련 MOC를 먼저 읽어 기존 지식 구조를 파악한다.
2. `raw/` 아래의 대상 원천 자료를 읽는다.
3. 핵심 주장, 사실, 코드/설계 패턴, 실무 적용 포인트, 모호한 점을 추출한다.
4. `wiki/sources/`에 자료별 요약 문서를 만든다.
5. 관련 `wiki/concepts/`, `wiki/entities/`, `wiki/projects/` 문서를 새로 만들거나 업데이트한다.
6. 새 자료가 기존 주장과 충돌하면 해당 문서에 `Contradictions` 또는 `Open Questions` 섹션을 만든다.
7. `wiki/index.md`와 관련 MOC를 업데이트한다.
8. `wiki/log.md`에 `## [YYYY-MM-DD] ingest | 제목` 형식으로 기록한다.
9. ingest가 끝난 raw 문서 frontmatter에 `ingested: true`, `ingested_at`, `ingest_refs`를 반영한다.

## Raw tagging 절차

사용자가 raw 파일 태그 정리를 요청하면 프로젝트 로컬 스킬 `.codex/skills/tag/SKILL.md`를 우선 적용한다.

1. `AGENTS.md`, `wiki/index.md`, 관련 MOC, `wiki/concepts/`를 읽어 현재 태그/개념 맥락을 파악한다.
2. 대상 raw 파일의 기존 frontmatter와 대표 본문을 읽는다.
3. 파일 이동, 삭제, 이름 변경은 하지 않는다.
4. raw 본문은 수정하지 않고 YAML frontmatter만 생성하거나 갱신한다.
5. 기존 frontmatter 필드는 보존하고 `tags`, `tagged`, `tagged_at`을 보강한다.
6. 기존 태그를 우선 재사용하되, 새 태그가 필요하다고 판단되면 간결하고 재사용 가능한 이름으로 추가한다.
7. `ingested`, `ingested_at`, `ingest_refs`는 명시적인 복구 요청이 없는 한 변경하지 않는다.
8. `ingested: true` 파일은 ingest 대상에서는 제외하지만 태그 정리 대상에서는 제외하지 않는다.

## Query 절차

사용자가 질문하면 Codex는 원천 자료부터 바로 뒤지지 말고 위키를 먼저 활용한다.

1. `wiki/index.md`를 읽는다.
2. 관련 MOC와 후보 문서를 읽는다.
3. 필요할 때만 `raw/` 원천 자료를 확인한다.
4. 답변에는 근거가 되는 위키 문서 또는 원천 자료 경로를 명시한다.
5. 답변이 재사용 가치가 있으면 사용자에게 확인 후 `wiki/questions/` 또는 `wiki/syntheses/`에 파일로 남긴다.

## Lint 절차

사용자가 위키 점검을 요청하면 다음 항목을 확인한다.

- 고아 문서: MOC나 index에서 연결되지 않은 문서
- 깨진 링크: 존재하지 않는 `[[링크]]`
- 중복 개념: 같은 개념을 다른 이름으로 설명하는 문서
- 오래된 주장: 새 자료와 충돌하거나 갱신이 필요한 설명
- 누락된 문서: 자주 언급되지만 독립 문서가 없는 개념
- 빈 frontmatter, 누락된 `updated`, 불명확한 `type`

점검 결과는 필요한 경우 `wiki/log.md`에 `## [YYYY-MM-DD] lint | 범위` 형식으로 남긴다.

## 변경 원칙

- `raw/` 원천 자료의 본문은 수정하지 않는다. 예외적으로 ingest 상태 추적용 frontmatter 필드만 갱신할 수 있다.
- `wiki/log.md`는 append-only로 다룬다. 기존 기록을 정정해야 하면 새 로그 항목을 추가한다.
- 기존 문서의 사용자 작성 내용을 덮어쓰지 않는다. 필요한 경우 새 섹션을 추가하거나 변경 근거를 남긴다.
- 대규모 재구성 전에는 먼저 제안하고 승인을 받는다.
- 비밀값, 접속 URL, 토큰, 개인 인증 정보는 위키에 저장하지 않는다.
