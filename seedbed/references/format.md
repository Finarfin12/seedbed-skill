# Seedbed format (version 1)

Shared with the Seedbed Obsidian plugin 0.7.0. The plugin's behavior is authoritative.

## A Seedbed note
A Markdown file whose frontmatter has `type: idea` or `type: concept`. Any other note is not Seedbed's: never list it, never write to it.

## Properties
| Property | Required | Values |
|---|---|---|
| `type` | yes | `idea` or `concept` |
| `status` | yes | `seed`, `growing`, `mature`, `discarded` (anything else makes the note "invalid") |
| `created` | yes | `YYYY-MM-DD` |
| `review` | yes (not for `discarded`) | `YYYY-MM-DD`, next review date |
| `gates` | no | list of `socratic`, `devil`, `feynman`, no duplicates |
| `source` | no | http/https URL |
| `origin` | no | text, for example `capture` or `skill` |

Never touch other properties (`tags`, `author`, ...). Do not write the old formats (`bengkel-*`, `seedbed-*`). If `.seedbed.json` maps custom property names, use those names instead.

## Status and review dates
`seed` to `growing` needs the Socratic gate for an `idea`. When status changes, `review` = today + interval: seed 7 days, growing 14, mature 30. A note is due when `review` is today or earlier (or empty, unless `discarded`).

## Socratic pass rule (fixed, not your judgment)
All 6 categories answered and each answer is at least 20 characters after trimming. Otherwise the gate does not pass and the status stays `seed`.

Categories, in this order, with the label to write: Clarification, Assumptions, Evidence, Viewpoints, Implications, Question about the question.

## Devil's Advocate pass rule
At least 4 of the 5 questions answered (not empty after trimming). Labels, in order: Assumptions, Opposite, Evidence, Opponent, Failure. A pass adds `devil` to `gates`; on a `growing` note it also sets `status: mature` and `review` = today + 30 days. A `seed` note is never promoted by it. For a `concept` the gate is optional.

## Feynman pass rule (skill version)
No gap left open (each gap closed by an explicit explanation of that term or step, see `references/feynman.md`). The plugin counts unexplained terms by pattern at 0 open gaps; the status change is the same: a `seed` concept becomes `growing`, `gates` gets `feynman`, `review` = today + 14 days. The trail uses a `[!feynman-hint]-` callout (title `Feynman check — date`), which the plugin does not analyze.

## Other callouts and the `premortem` gate
- `[!retrieval]-` (retrieval practice) and `[!premortem]-` (pre-mortem) follow the same line format. Neither changes `status` or `review`.
- `premortem` is a **new value of `gates`** written by the skill. Plugin 0.7.0 reads a note with it as valid but ignores the unknown value, and rewriting `gates` from the plugin (for example Promote) may drop it. Plugin 0.7.0's Feynman analyzer also counts the text of `[!retrieval]` and `[!premortem]` callouts for `concept` notes.

## Evidence callout (skill only)
`[!evidence]-` follows the same line format and changes no property (`status`, `review`, `gates` stay as they are). One callout per session; the plugin ignores its content (plugin 0.8.2 and later).

```markdown
> [!evidence]- Evidence — 2026-11-13
> **Claim** · AI · skill (<agent name>)
> <the user's claim, exactly>
>
> **Source 1** · A · journal
> <Title> — <Journal/publisher>, <Author>, <YYYY or YYYY-MM-DD>
> <https://doi.org/... or URL> (accessed 2026-11-13)
> Quote: "<word for word>"
> Does this support, contradict, or not relate to your claim? Which part?
> Answer: <the user's answer, verbatim>
>
> **Reflection**
> What would you change in your claim or idea now?
> Answer: <the user's answer, verbatim>
```
Tier `A` or `B`, then `journal`, `official`, `standard` or `blog` (`preprint` for preprints). Abstract only: `Quote (abstract):`. A source that could not be opened is never written. Details: `references/evidence.md`.

## Callout (the thinking trail)
Always **append at the end** of the note after one blank line. Never edit or delete existing text.

```markdown
> [!socratic]- Socratic session — 2026-11-12
> **Clarification** · AI · skill (Claude Code)
> What does "faster" mean here, and compared to what?
> Answer: <the user's words, exactly>
>
> **Assumptions** · AI · skill (Claude Code)
> ...
```

- Every line starts with `> `; an empty line inside is just `>`.
- Answer line: `Answer: ` followed by the first line of the answer; further answer lines just start with `> `.
- If an answer line starts with `[!`, write `\[!` so it cannot start a new callout.
- Source label: `AI · skill (<agent name>)`.

## New note files
Remove `\ / : * ? " < > | # ^ [ ]` from the title, cut to 80 characters, add a number if the name exists (`Title 2.md`). Never overwrite.
