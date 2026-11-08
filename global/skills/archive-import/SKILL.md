---
name: archive-import
description: Reviews, cleans up and moves a standalone repository or study project into one of the user's archive repositories with its commit history, then updates the archive README. Use when the user asks whether a repository should be merged into an archive, or asks to move, merge or import a repository or project into an archive repo.
---

# Importing a project into an archive repository

## 1. Decide whether it belongs

Recommend moving only when all of these hold; otherwise explain and suggest keeping it separate:

- It is the user's own study material (notes and code), not mainly a collection of other people's work.
- It is small. Large binaries (PDFs, videos, build outputs) stay in Git history forever, so check `gh api repos/<owner>/<repo>` (`diskUsage`) and the tree sizes first.
- It fits an existing area of the archive (`<area>/<topic>/<books|courses|training|conferences|official>/<source>/<project>`).

## 2. Review and clean up in the source repository first

Clone it next to the archive, then:

- **Names**: folders in kebab-case with two-digit order prefixes (`01-text-encoding/`); Python modules in snake_case; no spaces or Korean in paths.
- **Docs**: the landing document is `README.md` (rename `SUMMARY.md`, `INSTALL.md` and similar); remove author-only notes and placeholder links; convert `.txt` notes to Markdown next to the code they explain.
- **Third-party material**: remove slides, PDFs and copied example code whose rights belong to others; replace them with links to the official page or video. Confirm with the user when the origin is unclear — compare against the suspected source before calling something the user's own work.
- **Noise**: remove IDE folders (`.idea/`), build outputs and committed caches; trim `.gitignore` to what the project needs.
- **Verify**: run the examples (temporary virtualenv or Gradle build), check that every relative Markdown link and `#anchor` resolves, and fix references broken by renames. Use `git mv` so renames stay visible.

Commit the cleanup in the source repository with the archive's commit language.

## 3. Drop large blobs from history when needed

If removed files are still in history, rewrite a copy before importing so the archive never receives them:

```bash
git branch clean main
FILTER_BRANCH_SQUELCH_WARNING=1 git filter-branch -f --index-filter \
  'git rm -r -q --cached --ignore-unmatch -- "*.pdf" "<other paths>"' -- clean
```

Confirm the final tree of `clean` equals `main`, and measure the remaining blob size.

## 4. Import with history

```bash
cd <archive>
git subtree add --prefix=<area>/<topic>/<type>/<source>/<project> <path-to-source-clone> <branch>
```

Then update the archive README with the `archive-readme` skill, commit, and push (normal push — no history is rewritten in the archive).

## 5. Finish

- Deleting the original GitHub repository needs the `delete_repo` scope. If `gh repo delete` fails with 403, ask the user to delete it in the web UI (Settings → Danger Zone), then confirm with `gh repo view`.
- Remind the user that the local clone of the source repository can be removed afterwards.
