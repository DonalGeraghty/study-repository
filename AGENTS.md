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
2. **Use short, descriptive lowercase filenames with hyphens**, normally `<topic>.md`: `kubernetes.md`, `python.md`, `design-patterns.md`. Do not repeat broad category folder names such as `platform-engineering`, `languages`, or `frameworks` in filenames. Keep a short technology qualifier when it makes a generic topic clear, e.g. `java-collections.md` and `gcp-iam.md`; keep names such as `modern-java.md` as-is. Leaf-guide filenames must be unique across the vault (case-insensitively), so Obsidian note labels stay distinguishable. If a new topic would collide, add the shortest meaningful qualifier and update related names and links together when needed. Keep `README.md` for folder indexes and `AGENTS.md` for agent instructions.
3. **Link it from three places:** its parent `README.md`, the catalogue in `docs/README.md`, and (where a genuine relationship exists) the "Related Guides" section of related guides. Don't add cross-links that don't explain a real relationship.
4. **Use relative Markdown links**, never Obsidian `[[wikilink]]` syntax — `[text](./path.md)` / `[text](../path.md)`. This is what makes the repo render correctly on both GitHub and in Obsidian; wikilinks break on GitHub.
5. **Section structure is a loose convention, not a fixed template** — existing guides vary ("Common Failure Modes" vs "Common Misuses" vs "Common Mistakes", "Official References" vs "Further Reading", etc.). For a **new** guide, prefer the fuller pattern captured in `_templates/guide-template.md`: Quick Refresh → Worked Example → Common Failure Modes → Worked Prediction → Interview Questions → Answer Notes → Official References → Related Guides → `Return to [Parent](./README.md).`. Predictions should include an expected result and reasoning, or evaluation criteria for an open-ended design. Don't retrofit older guides to match just for consistency's sake.
6. **Add YAML frontmatter with a tag** at the very top of every new file, before the `# Title`:
   ```yaml
   ---
   tags:
     - <folder-path-under-docs>
   ---
   ```
   The tag is the path from `docs/` to the file's folder, slash-separated (e.g. `docs/platform-engineering/kafka.md` → `platform-engineering`; `docs/programming/languages/java/java-generics.md` → `programming/languages/java`). If the new file is a `README.md`, add a second tag, `moc`, alongside the folder tag.
7. **Verify links after any bulk edit.** There is no committed link-checker script (a one-off was used and discarded during the Obsidian migration) — if you touch many files at once, re-derive one rather than assuming links still resolve.
8. **Balance prose and lists for easier focus.** Prefer short, connected paragraphs for explanations and related failure modes, with one main idea per paragraph. Keep lists for navigation, genuine checklists, sequential steps and the interview-question callout. Avoid long runs of bullets, but do not replace useful lists with dense paragraphs.

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
- Keep the set small: four high-value questions per guide for now, phrased as real questions ("How would you...", "What happens when...", "Why does...") — never the older imperative checklist style ("explain X", "distinguish Y"). Select core mechanisms, useful distinctions and realistic application or failure cases rather than trying to quiz every detail.
- Follow the complete question callout with one `## Answer Notes` section, before references or navigation. Number the four answers to match question order, and keep each to a short paragraph. Give a direct answer for facts, essential checkpoints for mechanisms, and assumptions or trade-offs for design choices. For a prediction, state the exact result and why. These are self-marking notes, not scripts to memorise or a second copy of the lesson; link to an existing explanation when more detail is useful.
- This replaced several older, inconsistently-named sections (`Interview Checklist`, `Interview Exercise`, `Interview Approach`, `Practice Exercise(s)`, a bare `Practice`, `Readiness Checklist`, `Review Checklist`, `Completion Checklist`, `Quick Checklist`, `Quick Review Checklist`) that existed before this pass. If you see one of those headings anywhere, it's a regression — fold it into `## Interview Questions` rather than leaving it alongside.

## Obsidian layer

- `.obsidian/app.json`, `core-plugins.json`, `templates.json`, `graph.json` are committed shared vault config — keep them working for anyone who opens the repo root as a vault. `.obsidian/workspace.json` and other per-device state are gitignored; don't commit them if you see them appear.
- The graph view's default filter (`.obsidian/graph.json` → `"search": "-tag:#moc"`) hides `moc`-tagged index pages so the graph shows content relationships, not the hub-like README link fan-out. Keep tagging every `README.md` with `moc` so this keeps working.
- `_templates/guide-template.md` is the Obsidian core-Templates source for new guides — update it if the "preferred" section pattern in point 5 above changes, so the template stays the example to copy.
- No community plugins (Dataview, Templater, etc.) are assumed. Don't write instructions or content that depend on one being installed.

## What not to do

- Don't rename or restructure existing files/folders as a side effect of an unrelated change — the catalogue and every cross-link depends on stable paths. For an explicitly requested naming migration, update the naming rule, every affected link and filename reference, and verify link resolution in the same pass.
- Don't add frontmatter fields beyond `tags` (no `aliases`, `title`, dates, etc.) unless the user asks — the schema is intentionally minimal.
- Don't convert an ASCII diagram to Mermaid outside the policy in "Diagrams" above (a simple one-line chain stays ASCII) — that pass is done repo-wide, not an invitation to keep tidying leftover ASCII on sight.
- Don't rewrite a guide's other section headings (`Common Failure Modes` vs `Common Misuses` vs `Common Mistakes`, `Official References` vs `Further Reading`, etc.) for consistency; that variance is known and accepted, unlike the interview-question headings above, which were deliberately unified.
