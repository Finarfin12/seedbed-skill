# Seedbed note format (version 2)

Shared by the [Seedbed Obsidian plugin](https://github.com/Finarfin12/Seedbed) (0.8.0 and later) and this skill. **If this document and the plugin differ, the plugin's behavior is the truth** and this file gets fixed. Version 2 only adds to version 1: every version 1 note is still valid.

## 1. A Seedbed note
A Markdown file is a Seedbed note **only if `type` is `idea` or `concept`**. Every other note is ignored completely: not read, not written. (A digest note, `Seedbed digest YYYY-MM-DD.md`, has no `type`, so it is not a Seedbed note.)

## 2. Properties (frontmatter)
| Property | Required | Values | Notes |
|---|---|---|---|
| `type` | yes | `idea` \| `concept` | ownership marker |
| `status` | yes | `seed` \| `growing` \| `mature` \| `discarded` | any other value makes the note "invalid" |
| `created` | yes | `YYYY-MM-DD` | |
| `review` | yes (not `discarded`) | `YYYY-MM-DD` | next review date |
| `gates` | no | list of `socratic`, `devil`, `feynman`, `premortem` | gates passed. **Other values may appear and must be kept** (see below) |
| `source` | no | http/https URL | |
| `origin` | no | text, for example `capture`, `skill` | |

- Names above are the defaults. The plugin lets users map custom names; the skill reads them from `.seedbed.json` (section 7).
- Other properties (tags, author, ...) are never changed.
- **Unknown `gates` values (v2):** a reader that rewrites `gates` must keep every value it does not understand, in its original order, and must not add duplicates. The plugin does this since 0.8.0 (0.7.0 dropped them).
- Older formats the plugin still reads: `bengkel-*` (0.6) and `seedbed-*` (dev builds). The skill never writes them.

## 3. Status transitions
| From to | Requirement (when the gate is on) |
|---|---|
| `seed` to `growing` | idea: Socratic gate passed. concept: Feynman gate passed |
| `growing` to `mature` | Devil's Advocate gate passed (optional for a concept; plugin default: not required) |
| `mature` to `growing` | always allowed |
| anything to `discarded` | always allowed |
| `discarded` to `seed` | always allowed |

A passed gate's id is added to `gates` (no duplicates). `premortem` is an optional gate on a `mature` note: it never changes `status` or `review`.

## 4. Pass rules
| Gate | Rule |
|---|---|
| Socratic | 6 categories answered, each answer at least 20 characters after trimming |
| Devil's Advocate | at least 4 of 5 questions answered (not empty) |
| Feynman (plugin) | open unexplained-term gaps <= 0 (pattern-based count). Since 0.8.0 a term whose definition is circular (it only repeats the term) or empty (only filler words) still counts as a gap |
| Feynman (skill) | no gap left open: each gap closed by an explicit explanation of that term or step (see `seedbed/references/feynman.md`) |
| Pre-mortem (plugin 0.8.0 and skill) | at least 3 answers of 20+ characters (the plugin's minimum is configurable); adds `premortem` to `gates`, no status change. The callout is written even when the answers are not enough to pass |

Socratic labels: Clarification, Assumptions, Evidence, Viewpoints, Implications, Question about the question. Devil labels: Assumptions, Opposite, Evidence, Opponent, Failure. Pre-mortem labels (the plugin): Cause, Early signal, Assumption, Affected, Prevention (Indonesian: Penyebab, Sinyal awal, Asumsi, Terdampak, Pencegahan). The skill writes `Premortem`; readers accept any label.

## 5. Review intervals
seed 7 days, growing 14, mature 30, discarded none. On a status change, `review` = today + the interval of the new status.

Recall (retrieval practice): the skill leaves `review` as it is unless the user asks. The plugin (optional setting, on by default) sets `review` after a recall session to today + the interval of the status, adjusted by the user's own rating: `forgot` = interval / 2, `remembered` = interval x 1.5 (rounded, at least 1 day), `partly` or no rating = the normal interval.

## 6. Callouts (the thinking trail)
Always **appended at the end** of the note; existing text is never deleted.

```markdown
> [!socratic]- Socratic session — 2026-11-12
> **Clarification** · AI · skill (Claude Code)
> <question>
> Answer: <the user's words, exactly>
```

| Callout | Used for |
|---|---|
| `[!socratic]` | Socratic session |
| `[!devil]` | Devil's Advocate session |
| `[!feynman-hint]` | plugin: Feynman hints; skill: the Feynman check trail (the plugin does not analyze this type) |
| `[!premortem]` | pre-mortem session (plugin and skill) |
| `[!retrieval]` | recall practice (plugin and skill), layout below |
| `[!connect]` | answers about how this idea relates to another Seedbed idea (v2), layout below |
| `[!trigger]` / `[!quote]` | captures from the browser extension (`[!pemicu]` is the old name) |

Source label: the plugin writes `AI · local (...)`, `AI · cloud (...)` or `Bank`; the skill writes `AI · skill (<agent name>)`. A line of content that starts with `[!` is written as `\[!` (and read back without the backslash). Every line starts with `> `. The plugin's Indonesian UI writes `Jawaban:` and `Jawaban lama:` instead of `Answer:` and `Earlier answer:`; readers accept both.

### Retrieval layout (v2)
```markdown
> [!retrieval]- Retrieval — 2026-11-13
> **Recall** · Bank · forgot
> <the earlier question>
> Answer: <the new answer, exactly>
> Earlier answer: <the earlier answer, exactly>
```
The label line may end with the user's **own rating**: `remembered`, `partly` or `forgot` (always English, so tools can read it). Nobody scores the answer. A `[!retrieval]` callout is never used as a source for a new recall question.

### Connect layout (v2)
```markdown
> [!connect]- Connect — 2026-11-13
> **Kafe baca** · Bank
> Does this idea support, contradict, or relate to "Kafe baca"? Explain in your own words.
> Answer: <the user's words, exactly>
```
The label is the title of the other idea. Only notes with `type: idea` or `concept` may be candidates. A link is added only after the user asks for it: one line `Related: [[Title]]` at the end of the note, never duplicated, with `[ ] | # ^ \` and newlines removed from the title.

## 7. Skill configuration
Optional `.seedbed.json` at the root of the notes folder:

```json
{ "notesFolder": "Ideas", "conceptsFolder": "Concepts", "language": "auto", "properties": {} }
```

`properties` empty = default names; otherwise map them the same way as in the plugin settings.

## 8. File names
Remove `\ / : * ? " < > | # ^ [ ]`, cut to 80 characters, add a number if the name exists. Never overwrite a file.

## 9. Changes from version 1
- `gates` may hold other values (`premortem` is the first); readers must keep them.
- New callouts `[!connect]`; `[!retrieval]` gets a documented layout with an optional self-rating; `[!premortem]` may use per-category labels.
- The "Known interop limits" of plugin 0.7.0 no longer apply from plugin 0.8.0.
