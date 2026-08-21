---
name: tag
description: Use when adding or updating YAML frontmatter tags for raw Obsidian vault files, especially raw web clippings or pasted notes that must keep their body, path, and ingest state intact.
---

# Tag

## Purpose

Use this project-local skill to classify files under `raw/` by adding or updating YAML frontmatter only. The goal is to make accumulated web clippings and pasted notes discoverable before ingest, without promoting them into the wiki and without moving, deleting, or rewriting source content.

## Required Context

Before tagging raw files, read:

1. `AGENTS.md`
2. `wiki/index.md`
3. Relevant MOC files under `wiki/moc/`
4. Existing concept files under `wiki/concepts/`
5. The target raw files' existing frontmatter and enough body text to understand the subject

## Tagging Contract

For each target raw file:

1. Do not move, rename, delete, or relocate the file.
2. Do not rewrite body content. Only create or edit YAML frontmatter.
3. Preserve existing frontmatter fields such as `title`, `source`, `created`, `description`, `ingested`, `ingested_at`, and `ingest_refs`.
4. Preserve existing `tags` and add only useful missing tags.
5. Add `tagged: true` and `tagged_at: YYYY-MM-DD` after tagging.
6. Add `tag_basis` only when it helps explain ambiguous tags; keep it short.

If a raw file has no YAML frontmatter, create one at the top of the file. Use this minimum shape:

```yaml
---
tags: []
tagged: true
tagged_at: YYYY-MM-DD
ingested: false
---
```

## Tag Vocabulary Policy

Use existing vocabulary first.

- Prefer tags already present in this vault's wiki or raw frontmatter.
- Prefer terms represented by `wiki/index.md`, MOC entries, or `wiki/concepts/` pages.
- Use lowercase kebab-case for new technical tags unless the vault already uses another spelling.
- Keep collection/source tags such as `clippings` if they already exist.
- Do not create many one-off tags for a single document.
- Add a new tag when the file clearly needs a concept that is not covered by the existing vocabulary.
- Keep new tags concise, reusable, and consistent with the vault's naming style; avoid one-off tags.
- Report newly introduced tags in the final summary so they can be reviewed later.

## Ingest Relationship

Tagging and ingest are separate.

- `ingested: true` means the file should be excluded from future ingest batches.
- `ingested: true` does not exclude the file from tag cleanup or tag enrichment.
- This skill must not change `ingested`, `ingested_at`, or `ingest_refs` unless the user explicitly asks to repair ingest metadata.
- This skill must not create `wiki/sources/`, concept pages, MOCs, or log entries; use `ingest` for wiki promotion.

## Batch Workflow

For many raw files:

1. List candidate files and skip binary/attachment files.
2. Separate `ingested: true` from not-yet-ingested files in the report.
3. Read titles, descriptions, existing tags, and representative body text.
4. Reuse tags consistently across the batch.
5. Apply frontmatter-only edits.
6. Run `git status --short` and report changed raw files.

## Quality Bar

Before finishing:

- Confirm no raw file paths changed.
- Confirm no raw body content was intentionally changed.
- Confirm every tagged target has `tags`, `tagged: true`, and `tagged_at`.
- Confirm existing ingest metadata was preserved.
- Report newly introduced tags and any `tag_candidates` that were not promoted to confirmed tags.
- Run `git status --short` and summarize changed paths.

## Common Mistakes

| Mistake | Correction |
|---|---|
| Treating tag work as ingest | Do not create wiki pages or log entries. |
| Retagging with weak invented terms | Reuse current vault tags and concepts first; add new tags only when they are clearly reusable. |
| Removing `clippings` | Keep source/collection tags unless the user asks otherwise. |
| Editing the copied article body | Only touch YAML frontmatter. |
| Skipping ingested files entirely | Exclude them from ingest, not from tag cleanup. |
