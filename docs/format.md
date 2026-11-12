# Seedbed note format (version 1)

Shared by the [Seedbed Obsidian plugin](https://github.com/Finarfin12/Seedbed) (0.7.0) and this skill. **If this document and the plugin differ, the plugin's behavior is the truth** and this file gets fixed.

## 1. A Seedbed note
A Markdown file is a Seedbed note **only if `type` is `idea` or `concept`**. Every other note is ignored completely: not read, not written.

## 2. Properties (frontmatter)
| Property | Required | Values | Notes |
|---|---|---|---|
| `type` | yes | `idea` \| `concept` | ownership marker |
| `status` | yes | `seed` \| `growing` \| `mature` \| `discarded` | any other value makes the note "invalid" |
| `created` | yes | `YYYY-MM-DD` | |
| `review` | yes (not `discarded`) | `YYYY-MM-DD` | next review date |
| `gates` | no | list of `socratic`, `devil`, `feynman` | gates passed. The skill also writes `premortem` (see 6) |
| `source` | no | http/https URL | |
| `origin` | no | text, for example `capture`, `skill` | |

- Names above are the defaults. The plugin lets users map custom names; the skill reads them from `.seedbed.json` (section 7).
- Other properties (tags, author, ...) are never changed.
- Older formats the plugin still reads: `bengkel-*` (0.6) and `seedbed-*` (dev builds). The skill never writes them.

## 3. Status transitions
| From to | Requirement (when the gate is on) |
|---|---|
| `seed` to `growing` | idea: Socratic gate passed. concept: Feynman gate passed |
| `growing` to `mature` | Devil's Advocate gate passed (optional for a concept; plugin default: not required) |
| `mature` to `growing` | always allowed |
| anything to `discarded` | always allowed |
| `discarded` to `seed` | always allowed |

A passed gate's id is added to `gates` (no duplicates).

## 4. Pass rules
| Gate | Rule |
|---|---|
| Socratic | 6 categories answered, each answer at least 20 characters after trimming |
| Devil's Advocate | at least 4 of 5 questions answered (not empty) |
| Feynman (plugin) | open unexplained-term gaps <= 0 (pattern-based count) |
| Feynman (skill) | no gap left open: each gap closed by an explicit explanation of that term or step (see `seedbed/references/feynman.md`) |
| Pre-mortem (skill only) | at least 3 answers of 20+ characters; adds `premortem` to `gates`, no status change |

Socratic labels: Clarification, Assumptions, Evidence, Viewpoints, Implications, Question about the question. Devil labels: Assumptions, Opposite, Evidence, Opponent, Failure.

## 5. Review intervals
seed 7 days, growing 14, mature 30, discarded none. On a status change, `review` = today + the interval of the new status.

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
| `[!retrieval]`, `[!premortem]` | skill only: recall practice and pre-mortem |
| `[!trigger]` / `[!quote]` | captures from the browser extension (`[!pemicu]` is the old name) |

Source label: the plugin writes `AI · local (...)`, `AI · cloud (...)` or `Bank`; the skill writes `AI · skill (<agent name>)`. A line starting with `[!` inside an answer is written as `\[!`. Every line starts with `> `.

**Known interop limits (plugin 0.7.0):**
- A note with `premortem` in `gates` is read as valid, but the unknown value is ignored, and when the plugin rewrites `gates` (for example Promote or Reopen) `premortem` is dropped.
- The plugin's Feynman analyzer counts the text of `[!retrieval]` and `[!premortem]` callouts for `concept` notes.

## 7. Skill configuration
Optional `.seedbed.json` at the root of the notes folder:

```json
{ "notesFolder": "Ideas", "conceptsFolder": "Concepts", "language": "auto", "properties": {} }
```

`properties` empty = default names; otherwise map them the same way as in the plugin settings.

## 8. File names
Remove `\ / : * ? " < > | # ^ [ ]`, cut to 80 characters, add a number if the name exists. Never overwrite a file.
