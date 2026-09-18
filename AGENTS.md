# Agent Instructions

This repository is a personal technical study/interview handbook. It is Markdown-only, rendered two ways: on GitHub (as plain files) and as an Obsidian vault (repo root = vault root). Any agent editing this repo should preserve both.

## Repository shape

- `README.md` (root) — study methods and cross-subject learning paths.
- `docs/README.md` — the canonical, complete catalogue (every guide, one line each).
- `docs/<subject>/README.md` — one index per subject area, owns local learning order within that subject.
- `docs/<subject>/.../<guide>.md` — leaf guides.

Every folder under `docs/` has a `README.md`. When adding a new subfolder, add one.

## Adding or editing a guide

1. **One conceptual home.** Place the file under the subject folder it belongs to; don't split one topic across two locations.
2. **Link it from three places:** its parent `README.md`, the catalogue in `docs/README.md`, and (where a genuine relationship exists) the "Related Guides" section of related guides. Don't add cross-links that don't explain a real relationship.
3. **Use relative Markdown links**, never Obsidian `[[wikilink]]` syntax — `[text](./path.md)` / `[text](../path.md)`. This is what makes the repo render correctly on both GitHub and in Obsidian; wikilinks break on GitHub.
4. **Section structure is a loose convention, not a fixed template** — existing guides vary ("Common Failure Modes" vs "Common Misuses" vs "Common Mistakes", "Official References" vs "Further Reading", etc.). For a **new** guide, prefer the fuller pattern used by the recent messaging guides (`kafka.md`, `rabbitmq.md`, `pub-sub.md`, `amazon-sqs.md`, `amazon-sns.md`) and captured in `_templates/guide-template.md`: Quick Refresh → Worked Example → Common Failure Modes → Practice → Official References → Related Guides → `Return to [Parent](./README.md).`. Don't retrofit older guides to match just for consistency's sake.
5. **Add YAML frontmatter with a tag** at the very top of every new file, before the `# Title`:
   ```yaml
   ---
   tags:
     - <folder-path-under-docs>
   ---
   ```
   The tag is the path from `docs/` to the file's folder, slash-separated (e.g. `docs/platform-engineering/kafka.md` → `platform-engineering`; `docs/programming/languages/java/generics.md` → `programming/languages/java`). If the new file is a `README.md`, add a second tag, `moc`, alongside the folder tag.
6. **Verify links after any bulk edit.** There is no committed link-checker script (a one-off was used and discarded during the Obsidian migration) — if you touch many files at once, re-derive one rather than assuming links still resolve.

## Obsidian layer

- `.obsidian/app.json`, `core-plugins.json`, `templates.json`, `graph.json` are committed shared vault config — keep them working for anyone who opens the repo root as a vault. `.obsidian/workspace.json` and other per-device state are gitignored; don't commit them if you see them appear.
- The graph view's default filter (`.obsidian/graph.json` → `"search": "-tag:#moc"`) hides `moc`-tagged index pages so the graph shows content relationships, not the hub-like README link fan-out. Keep tagging every `README.md` with `moc` so this keeps working.
- `_templates/guide-template.md` is the Obsidian core-Templates source for new guides — update it if the "preferred" section pattern in point 4 above changes, so the template stays the example to copy.
- No community plugins (Dataview, Templater, etc.) are assumed. Don't write instructions or content that depend on one being installed.

## What not to do

- Don't rename or restructure existing files/folders as a side effect of an unrelated change — the catalogue and every cross-link depends on stable paths.
- Don't add frontmatter fields beyond `tags` (no `aliases`, `title`, dates, etc.) unless the user asks — the schema is intentionally minimal.
- Don't convert existing ASCII-art diagrams to Mermaid or rewrite existing section headings for consistency unless asked; both are known, accepted variance, not defects.
