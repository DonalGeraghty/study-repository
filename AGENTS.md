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
2. **Name the file `<immediate-parent-folder-name>-<topic>.md`**, e.g. `java-generics.md` under `programming/languages/java/`, `gcp-iam.md` under `platform-engineering/cloud/gcp/`. Skip the prefix only if the folder name already appears in the topic name (e.g. `modern-java.md` under `java/` stays as-is). This exists because Obsidian's graph view and quick switcher label/match notes by **filename only** — never folder path, never the H1 heading — so a bare topic name that looks unambiguous today (`collections.md`) collides the moment a sibling folder reuses it (a future `csharp-collections.md`). Every leaf guide in the repo was renamed to this pattern in one pass; keep new files consistent with it.
3. **Link it from three places:** its parent `README.md`, the catalogue in `docs/README.md`, and (where a genuine relationship exists) the "Related Guides" section of related guides. Don't add cross-links that don't explain a real relationship.
4. **Use relative Markdown links**, never Obsidian `[[wikilink]]` syntax — `[text](./path.md)` / `[text](../path.md)`. This is what makes the repo render correctly on both GitHub and in Obsidian; wikilinks break on GitHub.
5. **Section structure is a loose convention, not a fixed template** — existing guides vary ("Common Failure Modes" vs "Common Misuses" vs "Common Mistakes", "Official References" vs "Further Reading", etc.). For a **new** guide, prefer the fuller pattern used by the recent messaging guides (`platform-engineering-kafka.md`, `platform-engineering-rabbitmq.md`, `platform-engineering-pub-sub.md`, `platform-engineering-amazon-sqs.md`, `platform-engineering-amazon-sns.md`) and captured in `_templates/guide-template.md`: Quick Refresh → Worked Example → Common Failure Modes → Practice → Official References → Related Guides → `Return to [Parent](./README.md).`. Don't retrofit older guides to match just for consistency's sake.
6. **Add YAML frontmatter with a tag** at the very top of every new file, before the `# Title`:
   ```yaml
   ---
   tags:
     - <folder-path-under-docs>
   ---
   ```
   The tag is the path from `docs/` to the file's folder, slash-separated (e.g. `docs/platform-engineering/platform-engineering-kafka.md` → `platform-engineering`; `docs/programming/languages/java/java-generics.md` → `programming/languages/java`). If the new file is a `README.md`, add a second tag, `moc`, alongside the folder tag.
7. **Verify links after any bulk edit.** There is no committed link-checker script (a one-off was used and discarded during the Obsidian migration) — if you touch many files at once, re-derive one rather than assuming links still resolve.

## Diagrams

Guides use Mermaid diagrams (rendered natively by both GitHub and Obsidian, no plugin needed) to make process, sequence, hierarchy, and state-shaped content visual. Every guide got a pass for this already — 63 diagrams across 45 files — so treat the pattern below as established, not a one-off.

Pick the Mermaid type from the content's actual shape:

| Content shape | Mermaid type |
| --- | --- |
| Interface/class hierarchy, polymorphism | `classDiagram` |
| Multi-party protocol or call sequence | `sequenceDiagram` |
| Lifecycle with named states | `stateDiagram-v2` |
| Pipeline, branching process, org/resource hierarchy | `flowchart` |
| Table relationship / cardinality | `erDiagram` |
| Git branch/merge topology | `gitGraph` |

- **New diagram**: add a `mermaid` code block alongside existing prose where content has a genuine shape (a lifecycle, a multi-party exchange, an interface hierarchy, a branch, a multi-stage pipeline) and has no diagram today. Insert it after the section's intro sentence, don't remove any surrounding text.
- **Upgrade**: replace an existing ASCII `text` diagram with an equivalent `mermaid` one only when it's a genuine like-for-like improvement — mainly diagrams that already branch, fork, or converge, which ASCII renders awkwardly. Leave a simple one-line chain (`A -> B -> C`) as ASCII; converting it is diagram-for-diagram's-sake, not a real improvement.
- **No diagram**: leave pure reference/syntax/table files alone. Not every guide needs one, and a forced diagram for content with no real shape (a flat command table, a syntax cheat sheet) adds noise, not clarity.

## Interview Questions

Every leaf guide (not README/index files — those are navigation, not something to quiz on) ends with exactly one `## Interview Questions` section, placed where an "Interview Checklist"/"Readiness Checklist"/"Practice Exercise" section used to sit (after the last main content section, before "Official References"/"Further Reading"/"Related Guides"/the closing `Return to...` line):

```markdown
## Interview Questions

> [!question] Interview Questions
> - Question 1?
> - Question 2?
```

- Use Obsidian's `[!question]` callout syntax exactly as shown. Obsidian renders it as a styled callout; GitHub doesn't recognise `question` as one of its five alert keywords, so it degrades to a plain blockquote there — an accepted, deliberate trade-off for the better Obsidian rendering.
- 4–8 items, phrased as real questions ("How would you...", "What happens when...", "Why does...") — never the older imperative checklist style ("explain X", "distinguish Y").
- This replaced several older, inconsistently-named sections (`Interview Checklist`, `Interview Exercise`, `Interview Approach`, `Practice Exercise(s)`, a bare `Practice`, `Readiness Checklist`, `Review Checklist`, `Completion Checklist`, `Quick Checklist`, `Quick Review Checklist`) that existed before this pass. If you see one of those headings anywhere, it's a regression — fold it into `## Interview Questions` rather than leaving it alongside.

## Obsidian layer

- `.obsidian/app.json`, `core-plugins.json`, `templates.json`, `graph.json` are committed shared vault config — keep them working for anyone who opens the repo root as a vault. `.obsidian/workspace.json` and other per-device state are gitignored; don't commit them if you see them appear.
- The graph view's default filter (`.obsidian/graph.json` → `"search": "-tag:#moc"`) hides `moc`-tagged index pages so the graph shows content relationships, not the hub-like README link fan-out. Keep tagging every `README.md` with `moc` so this keeps working.
- `_templates/guide-template.md` is the Obsidian core-Templates source for new guides — update it if the "preferred" section pattern in point 5 above changes, so the template stays the example to copy.
- No community plugins (Dataview, Templater, etc.) are assumed. Don't write instructions or content that depend on one being installed.

## What not to do

- Don't rename or restructure existing files/folders as a side effect of an unrelated change — the catalogue and every cross-link depends on stable paths. (The repo-wide rename to the `<folder>-<topic>.md` pattern above was a deliberate, planned, one-time exception with every link updated in the same pass — it's not licence to rename things casually going forward.)
- Don't add frontmatter fields beyond `tags` (no `aliases`, `title`, dates, etc.) unless the user asks — the schema is intentionally minimal.
- Don't convert an ASCII diagram to Mermaid outside the policy in "Diagrams" above (a simple one-line chain stays ASCII) — that pass is done repo-wide, not an invitation to keep tidying leftover ASCII on sight.
- Don't rewrite a guide's other section headings (`Common Failure Modes` vs `Common Misuses` vs `Common Mistakes`, `Official References` vs `Further Reading`, etc.) for consistency; that variance is known and accepted, unlike the interview-question headings above, which were deliberately unified.
