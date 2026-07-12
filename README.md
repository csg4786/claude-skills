# Claude Skills Archive

Personal archive of custom Claude skills, with version history.

## Structure

```
skills/
  <skill-name>/
    current/          — the latest version's source (SKILL.md + any bundled resources)
    versions/
      v1/              — source + packaged .skill file for this version
      v2/
      ...
    CHANGELOG.md       — what changed in each version and why
```

- **`current/`** always mirrors the newest `versions/vN/`. It exists so you (or Claude) can grab the latest source directly without digging into version folders.
- **`versions/vN/`** is a permanent snapshot — never edit these after the fact. If a skill needs a fix, that's a new version (`vN+1`), not an edit to an old one.
- Each version folder includes both the raw `SKILL.md` (and any `references/`, `scripts/`, `assets/`) for readable diffs, and the packaged `.skill` zip file for re-uploading to Claude.ai directly.

## Workflow for updating a skill

1. Ask Claude to update the skill (via the `skill-creator` skill), same as usual.
2. Before packaging the new version, copy the current `versions/vN/` forward as-is (already done for existing versions) and bump to `vN+1`.
3. Update `current/` to match the new version.
4. Add an entry to that skill's `CHANGELOG.md` describing what changed and why.
5. Commit with a message like `exam-prep-coach: v2 - two-stage diagnostic, fixes thin sample size`.
6. Push, then download the new `.skill` file from `versions/vN+1/` and click "Save skill" in Claude.ai to actually install the update — pushing to GitHub does not update what's installed in Claude.ai; it's the archive/version history, not the live sync.

## Rolling back

If a new version misbehaves, the old `.skill` file is sitting untouched in its own `versions/vN/` folder — download it and re-save it in Claude.ai to revert. No need to reconstruct anything from memory or chat history.
