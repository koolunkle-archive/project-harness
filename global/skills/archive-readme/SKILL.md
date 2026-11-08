---
name: archive-readme
description: Writes or updates the root README of a learning-archive repository (programming-archieve, web-archive, native-mobile-archive, cross-platform-archive and similar) in the user's standard Korean format, with a linked project table and verified links. Use when the user adds a project to an archive repo, asks to improve an archive README, or asks for consistency across archive repositories.
---

# Archive README format

## Structure (in this order)

```markdown
# <Name> Archive

> 도서, 강의, 공식 자료 등을 통해 학습한 <분야> 프로젝트를 정리한 저장소입니다.

## Tech Stack

(shields.io badges, style=flat-square, one per line)

## Repository Structure

### <Section, e.g. Frontend / Languages / Android>

| 프로젝트 | 유형 | 출처 | 내용 |
| --- | --- | --- | --- |
| [folder-name](relative/path/to/folder) | 도서 | ... | ... |
```

- Write in Korean, in a neutral written style (`~합니다`, `~입니다`); avoid casual phrasing such as `~하면 됩니다`.
- Keep the intro sentence generic enough to stay true when new projects are added. List the source types the repository actually contains (e.g. `도서, 강의, 교육과정을 통해` or `도서, 강의, 공식 자료 등을 통해`), and keep an existing intro unless it no longer matches the contents.
- Add a leading `언어` or `분야` column when one section mixes several languages or fields.

## Table rules

- **프로젝트**: the actual folder name, linked with a relative path (works on GitHub and survives repository renames).
- **유형**: one of `도서`, `강의`, `교육`, `공식 자료`, `컨퍼런스`, `실습`, `개인`.
- **출처**: `플랫폼 - 정식 강의명` (e.g. `인프런 - 개발하는 정대리 코틀린 기초 문법`), the official book title, or the institution name. Do not add instructor names when the platform is already the source; YouTube channels are written as `채널명 (YouTube) - 강의명`. Use `-` when there is no source.
- **내용**: what was actually built or studied, checked against the code and sub-READMEs — never inferred from the folder name alone.
- One row per course, book or training program. Group many sub-projects into one row and summarize them in 내용.
- Avoid inline HTML such as `<br>`; keep each cell on one line.

## Before finishing

1. Ask for official titles you cannot verify instead of guessing; state any guess explicitly.
2. Verify every relative link points to an existing folder.
3. Keep the same table style (`| --- |` separators) to avoid markdownlint MD060 warnings.
4. Commit with the repository's message convention (see the `commit-history` skill).
