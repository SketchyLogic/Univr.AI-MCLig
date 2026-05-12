# /LINT_GLOSSARY

Audits all files in `wiki/glossary/` and reassigns `readOrderIndex` values to reflect the real dependency graph. Content is never modified — only frontmatter and structure are touched.

---

## What this command does

1. **Read every file** in `wiki/glossary/` and note its filename, type tags, `readOrderIndex`, and any wiki-links (`[[term]]`) it contains.
2. **Build the dependency graph**: an entry that links to another entry depends on it. Circular links are flagged as errors.
3. **Reassign `readOrderIndex`** for every entry according to the rules below. Existing values may change.
4. **Check for structural problems** (see checklist).
5. **Write all changed files** (frontmatter only — `LastUpdateAt` and `readOrderIndex` updated; `GeneratedBy` updated to current model ID if the file was AI-generated).
6. **Report** a numbered list of every change and every problem found.
7. **Append** a summary line to `wiki/log.md`.

---

## readOrderIndex assignment rules

`readOrderIndex` is an integer reading-order hint. Multiple entries may share the same value (they are at the same conceptual layer). Gaps between values are fine.

| Value range | Meaning | Assignment rule |
|-------------|---------|-----------------|
| Negative (`< 0`) | Prerequisite — background knowledge the reader must have **before** reading the paper | Entry carries the `PREREQUISITE` tag **and** has no dependencies on non-prerequisite entries. Assign `-1` for direct prerequisites; `-2` or lower for foundations of foundations. |
| `0` | Entry is accessible as soon as all prerequisites are satisfied; no non-prerequisite dependencies | Entry has no `[[wiki-links]]` pointing to entries with `readOrderIndex > 0`, and all its dependencies are prerequisites (`< 0`) or also `0`. |
| Positive (`> 0`) | Builds on earlier glossary entries; higher value = later in the reading sequence | Set to `max(readOrderIndex of all linked entries) + 1`. If an entry links only to prerequisites, its index is `0` unless its content is clearly advanced material, in which case use `1`. |

### Tie-breaking and overrides

- If an entry's `PREREQUISITE` tag contradicts a positive dependency chain, flag the inconsistency and ask the user before changing the tag.
- If an entry has no wiki-links and carries `PREREQUISITE`, keep it at its current negative value (or assign `-1` if missing).
- If an entry has no wiki-links and no `PREREQUISITE` tag, assign `0`.

---

## Structural checklist (report problems, do not auto-fix content)

- [ ] Every file has `readOrderIndex` — add the field if missing (assign via the rules above).
- [ ] Every file has all required fields: `CreatedAt`, `LastUpdateAt`, `LastReviewAt`, `ReviewerIds`, `OwnerIds`, `IssueNotes`, `GeneratedBy`.
- [ ] Every file has at least one recognised type tag: `__DEFINITION`, `__CONCEPT`, `__EXECUTABLE`, or `PREREQUISITE`.
- [ ] No file combines multiple terms (filename should name exactly one concept).
- [ ] No circular wiki-link dependencies.
- [ ] No broken `[[wiki-links]]` — every linked filename must exist in `wiki/glossary/`.
- [ ] Files with `GeneratedBy` set and `LastReviewAt: null` — list separately as "AI-generated, unreviewed."

---

## Output format

Report findings as a numbered list grouped into three sections:

```
### readOrderIndex changes
1. `Term.md`: -1 → 0  (reason: no PREREQUISITE tag, no dependencies)
2. `Other term.md`: 0 → 2  (reason: depends on Term.md which is now 0, plus Concept.md at 1)

### Structural problems
3. `Missing field.md`: missing `IssueNotes` field — add with value `null`
4. `Broken link.md`: [[Nonexistent term]] — no matching file in wiki/glossary/

### AI-generated and unreviewed
5. `Term.md` (created 2026-05-10)
```

If there are no findings in a section, write "None."
