# Changelog

## 0.3.0
- **Feynman is stricter:** a gap closes only when the answer explicitly explains that term (or gives a reason for that leap); otherwise up to 2 follow-up questions per gap, then the gap is open and the gate does not pass while any gap is open. The agent still never judges correctness. Examples are in `references/feynman.md`.
- **New modes:** retrieval (recall practice inside review, `[!retrieval]` callout), connect (asks whether other Seedbed notes relate; links only after a yes), digest (weekly summary, no file unless asked), premortem (`[!premortem]` callout, adds `premortem` to `gates`, no status change).
- Search by frontmatter (`type:`) so non-Seedbed notes are never opened or named.
- Moved to its own repository (history kept from the Seedbed repository's `skill/s1` branch). MIT license. Claude Code plugin marketplace manifest added.

## 0.2.0
- Devil's Advocate, Feynman and setup modes; `templates/concept.md`.

## 0.1.0
- First version: capture, socratic, review.
