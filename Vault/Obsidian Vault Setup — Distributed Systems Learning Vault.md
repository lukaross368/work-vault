
A one-session setup to turn reading (6.5840, papers, books) and lab work into a reusable project engine. Work through the **Setup checklist** at the bottom; the sections above are the reference.

---

## 1. The model in one line

One pool of plain-markdown notes, enriched with consistent **properties**, surfaced through three **Bases** (Concepts = what you know, Seeds = what you could build, Patterns = how you build it), and verified by writing blog posts.

---

## 2. Plugins

**Core (toggle on in Settings → Core plugins):**
- Properties view
- Bases
- Templates *(optional — Templater replaces it)*
- Daily notes

**Community (Settings → Community plugins → Browse):**
- **Templater** — dynamic note scaffolds (auto-dates, prompts)
- **QuickAdd** — one-hotkey capture from anywhere
- **Obsidian Git** — auto-commit the vault for backup + version history

**Browser:**
- **Obsidian Web Clipper** (official extension) — clip HTML papers/articles with metadata

Install order: core first, then Templater, then QuickAdd (it depends on having templates to point at), then Git last.

---

## 3. Folder structure

Folders track a note's **role/lifecycle**, never its topic. Topic is a property, queried by Bases — so you never agonise over "which folder does consensus go in."

```
Vault/
├── Inbox/          # raw capture lands here, unsorted
├── Notes/          # all atomic notes: concept + pattern (flat, split by `type`)
├── Sources/        # one literature note per paper / lecture / book
├── MOCs/           # maps of content — curated topic hubs
├── Projects/       # one note per build (flux-snapshot, scheduler, ...)
├── Bases/          # the .base files + a Dashboard note
├── Templates/      # Templater templates
└── Attachments/    # PDFs, images
```

Concept and pattern notes live together in `Notes/` and are separated by the `type` property, not by folder. Split them later only if you find a reason to.

---

## 4. Property schema (the canonical reference)

Multi-word property names use **snake_case** (hyphens get misread as minus signs in Bases filter expressions).

| Property | Used by | Values |
|---|---|---|
| `type` | all | `concept` \| `pattern` \| `source` \| `moc` \| `project` |
| `topic` | concept, pattern, moc | `consensus` \| `replication` \| `membership` \| `consistency` \| `scheduling` \| `ebpf` \| `networking` \| `storage` \| `observability` |
| `status` | concept | `captured` \| `distilled` \| `published` |
| `status` | pattern | `tested` \| `reusable` |
| `status` | project | `idea` \| `building` \| `shipped` |
| `project_surface` | concept | free text — "where could this plug into k8s/eBPF?" |
| `source` | concept, pattern | link to a `Sources/` note, e.g. `"[[Raft paper]]"` |
| `lang` | pattern | `go` \| `ebpf` \| `bash` |
| `aliases` | any | list, e.g. `[leader election]` |
| `tags` | any | list — see §5 |
| `created` | all | date (auto-filled by Templater) |

The one to never skip is **`project_surface`** on concept notes — it's what auto-populates the Seeds base.

---

## 5. Tag taxonomy

**Rule #1:** Properties hold structured, known dimensions (type, topic, status). Tags hold only the fuzzy, cross-cutting stuff properties can't. Never tag what you already store as a property.

**Rule #2:** Controlled vocabulary, max 5 tags per note. Review the tag pane every few weeks and merge sprawl.

Two families, both nested so they group in the tag pane:

**Theme tags** — deep ideas that cut across topics:
```
#theme/fault-tolerance
#theme/consistency
#theme/causality
#theme/quorum
#theme/gossip
#theme/partial-failure
#theme/time-ordering
#theme/performance
```

**Flag tags** — a tiny fixed workflow set:
```
#flag/fuzzy         # didn't fully grasp it — comprehension gap to revisit
#flag/revisit       # come back to this
#flag/to-implement  # itching to build this
#flag/blog-idea     # candidate post
```

`#flag/fuzzy` is the important one: it feeds the "Comprehension gaps" view so your not-yet-understood material can't quietly disappear.

---

## 6. Templates

Drop each into `Templates/`. They assume Templater is enabled.

### Concept note
```markdown
---
type: concept
topic: 
source: 
status: captured
project_surface: 
aliases: 
tags: []
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.file.title %>

## Idea
<!-- One technique, in YOUR words. If you can't write this without the paper open, add #flag/fuzzy. -->

## Why it works / why it matters

## Where it could plug in
<!-- Then copy the key phrase up into the project_surface property. -->

## Links
- Related: 
- Patterns: 
- Source: 
```

### Pattern note
```markdown
---
type: pattern
topic: 
lang: go
source: 
status: tested
tags: []
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.file.title %>

## Problem it solves

## The pattern
` ` `go
// your reusable, already-debugged snippet
` ` `

## Gotchas

## Used in
- Concept: 
- Project: 
```
*(remove the spaces inside the code fences — they're only here to keep this guide readable)*

### Source / literature note
```markdown
---
type: source
kind: paper          # paper | lecture | book
authors: 
year: 
url: 
status: reading
tags: []
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.file.title %>

## One-line summary

## Raw highlights & reactions
<!-- Messy is fine. This is the landing zone, not a finished note. -->

## Concepts extracted
- [[ ]]
```

### MOC (map of content)
```markdown
---
type: moc
topic: 
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.file.title %> MOC

> The path I'd actually take through this topic.

## Core concepts
- [[ ]]

## Patterns
- [[ ]]

## Key sources
- [[ ]]
```

### Project note
```markdown
---
type: project
status: idea         # idea | building | shipped
repo: 
blog: 
tags: []
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% tp.file.title %>

## Goal

## Algorithm(s)
- [[ ]]

## Extension point (controller / scheduler / CNI / CRD)

## Concepts & patterns to reuse
- 

## Log
```

---

## 7. Bases

Create three `.base` files in `Bases/`. Easiest path: command palette → **"Bases: Create new base"**, build the filter with the dropdowns, then click the `</>` (advanced) icon and paste the YAML below to match exactly. Embed any base in your Dashboard note with `![[Concepts.base]]`.

### Concepts.base — what you know
```yaml
filters:
  and:
    - 'type == "concept"'
views:
  - type: table
    name: All concepts
    order:
      - file.name
      - topic
      - status
      - project_surface
  - type: table
    name: Not yet written up
    filters:
      and:
        - 'status != "published"'
    order:
      - file.name
      - topic
      - status
```

### Seeds.base — what you could build (the auto-backlog)
```yaml
filters:
  and:
    - 'type == "concept"'
    - 'project_surface != ""'
views:
  - type: table
    name: Project seeds
    order:
      - file.name
      - project_surface
      - topic
      - source
```

### Patterns.base — how you build it
```yaml
filters:
  and:
    - 'type == "pattern"'
views:
  - type: table
    name: Patterns
    order:
      - file.name
      - lang
      - topic
```

### Comprehension gaps (add as a second base, or a view on Concepts)
```yaml
filters:
  and:
    - 'file.hasTag("flag/fuzzy")'
views:
  - type: table
    name: Revisit — didn't fully grasp
    order:
      - file.name
      - topic
      - source
```

Build a **Dashboard.md** in `Bases/` that embeds Seeds first (your backlog), then Comprehension gaps, then Concepts. Make it your vault's home note.

---

## 8. Daily operating workflow

1. **Capture** → QuickAdd hotkey or Web Clipper dumps into `Inbox/`. Rough and fast. Don't file yet.
2. **Distill** → in the same sitting or next day, turn inbox material into atomic notes in `Notes/`, in your own words. One technique per concept note.
3. **Enrich** → fill properties, add 2–4 links to related notes, write the `project_surface` line, add ≤5 tags. Flag anything shaky with `#flag/fuzzy`.
4. **Source** → for each paper, keep one literature note in `Sources/` as the landing zone; spawn concept notes from it.
5. **Pattern** → when a lab problem cracks, capture the Go/eBPF shape immediately as a pattern note.
6. **Build** → open Seeds base, pick one, follow its links into the concept + pattern notes. Promote it to a `Projects/` note.
7. **Publish** → graduate strong concept notes into blog posts; walk `status` to `published`. If you can't write it without the paper, it's not understood — back to step 2.

---

## 9. Setup checklist (one session)

- [x] Enable core plugins: Properties, Bases, Daily notes
- [x] Install Templater, QuickAdd, Obsidian Git
- [x] Create folders: Inbox, Notes, Sources, MOCs, Projects, Bases, Templates, Attachments
- [x] Add the 5 templates to `Templates/`
- [x] Point Templater at the `Templates/` folder (Settings → Templater → Template folder)
- [x] Create QuickAdd "New concept note" macro → template into `Notes/`, bind a hotkey
- [x] Repeat QuickAdd for pattern / source / project
- [x] Create the 3 Bases (+ comprehension-gaps view)
- [x] Build `Bases/Dashboard.md`, embed the bases, set as home note
- [x] Install Web Clipper browser extension
- [x] Enable Obsidian Git auto-commit (e.g. every 10 min) and connect a private repo
- [ ] Seed the vault (see §10) so it isn't empty

---

## 10. First-week seeding

Don't leave it empty — an empty system feels pointless and dies. In the first week:

- Create 5 MOCs: Consensus, Replication, Membership, eBPF observability, Scheduling.
- As you start 6.5840 Lab 1 and the Raft reading, capture your first concept notes: *leader election*, *quorum intersection*, *log matching*, *randomized election timeouts*, *committing entries from previous terms*.
- Give each a `project_surface` line and watch the Seeds base populate itself.
- After the Raft lab, write your first pattern notes from the concurrency shapes you fought with.

By the end of week one the three Bases have real rows in them, and the system is an engine instead of a folder.
```