---
name: ingest
description: Use when ingesting raw source documents into this Obsidian LLM Wiki, especially files under raw/ that must update wiki sources, concepts, MOCs, index, and log.
---

# Ingest

## Purpose

Use this project-local skill to integrate raw source documents into this vault's persistent LLM Wiki. The goal is not only to summarize sources, but to compound the wiki by updating related concept pages, MOCs, `wiki/index.md`, and `wiki/log.md`.

## Required Context

Before writing wiki files, read:

1. `AGENTS.md`
2. `wiki/index.md`
3. `wiki/log.md`
4. Relevant MOC files under `wiki/moc/`
5. Existing concept, project, or source pages that match the topic

For Kafka-related ingest, also inspect:

- `wiki/moc/Data MOC.md`
- `wiki/concepts/Kafka.md`
- `wiki/projects/주문 처리 시스템 최적화.md`

## Ingest Contract

For each raw source:

1. Treat files under `raw/` as immutable source material, except for ingest status fields in YAML frontmatter.
2. Create or update one source summary under `wiki/sources/`.
3. Use a stable source filename, preferably `YYYY-MM-DD-<source-title>.md`.
4. Include frontmatter with `type: source`, `status`, `created`, `updated`, `tags`, and `source_path`.
5. Summarize key claims, practical implications, examples, limitations, and open questions.
6. Link to related concept, project, entity, question, or synthesis pages using Obsidian `[[links]]`.
7. After successful ingest, update the raw source frontmatter with `ingested: true`, `ingested_at: YYYY-MM-DD`, and `ingest_refs` pointing to the generated source summary. If the source has not yet been ingested, use `ingested: false`.

For the wiki as a whole:

1. Update relevant concept pages in `wiki/concepts/`.
2. Create new concept pages only when a concept is important enough to be queried later.
3. Update relevant MOCs so a human can navigate to the new knowledge.
4. Update `wiki/index.md` so a future agent can discover the new pages.
5. Append to `wiki/log.md` using `## [YYYY-MM-DD] ingest | <title or batch name>`.
6. If sources contradict existing claims, add a `Contradictions` or `Open Questions` section near the affected claim.

## Batch Ingest

When multiple raw files are part of one topic, process them as a batch:

- Read all source titles and headings first.
- Decide whether each file needs its own source summary.
- Update shared concept pages once after reading the full batch, not separately for every file.
- Add one batch entry to `wiki/log.md` listing the processed source paths.

## Kafka Batch Defaults

For Kafka source batches in this vault, prefer these target pages unless the source content suggests better ones:

- `wiki/sources/YYYY-MM-DD-kafka-introduction.md`
- `wiki/sources/YYYY-MM-DD-kafka-design.md`
- `wiki/sources/YYYY-MM-DD-kafka-kraft-vs-zookeeper.md`
- `wiki/concepts/Kafka.md`
- `wiki/concepts/KRaft.md`
- `wiki/concepts/ZooKeeper.md`
- `wiki/concepts/Kafka 브로커.md`
- `wiki/concepts/Kafka 토픽과 파티션.md`
- `wiki/moc/Data MOC.md`

Create only the concept pages that the source material actually supports.

## Quality Bar

Before finishing an ingest:

- Confirm no `raw/` body content was modified; only ingest status frontmatter may change.
- Confirm every ingested raw source has `ingested: true`, `ingested_at`, and `ingest_refs`.
- Confirm every new source summary is linked from `wiki/index.md` or a MOC.
- Confirm every newly created concept page has at least one inbound link from a MOC, index, source summary, or related concept.
- Confirm `wiki/log.md` has a new append-only entry.
- Run `git status --short` and report changed paths.

## Common Mistakes

| Mistake | Correction |
|---|---|
| Only creating summaries | Also update concepts, MOCs, index, and log. |
| Starting from raw search only | Read `wiki/index.md` first to preserve accumulated structure. |
| Moving or rewriting source files | Leave raw body content unchanged; only update ingest status frontmatter. |
| Forgetting raw ingest status | Set `ingested: true`, `ingested_at`, and `ingest_refs` after successful ingest. |
| Creating too many thin concept pages | Create concept pages only for reusable ideas. |
| Forgetting contradictions | Mark conflicts or uncertainty in the affected wiki page. |
