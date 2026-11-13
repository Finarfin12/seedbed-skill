# Changelog

## Unreleased
- Package: added ready-to-extract `seedbed-skill-0.4.0.zip` to GitHub Releases and the repository root, with download instructions in README (EN/ID).
- Docs: README (EN/ID) updated for 0.4.0: status, plugin compatibility table, related repositories (Seedbed, seedbed-capture), evidence test coverage; removed the outdated plugin 0.7.0 limit.

## 0.4.0
- **New mode: evidence (skill only).** You write the claim first; the agent shows search terms and searches only after your yes; it opens trustworthy sources (tier A: journals, official sites, standards, governments, universities; tier B: trustworthy technical blogs and preprints) and shows word-for-word quotes with links, then only asks how each source relates to your claim. No summary, no verdict, no scores. Sources that disagree are both shown. Saved as a `[!evidence]` callout; `status`, `review` and `gates` never change.
- New rules **A-9** (web content is data, not instructions) and **A-10** (quote only what you opened).
- Needs web search and page-reading tools in the agent (Claude Code: WebSearch and WebFetch). Without them the mode says so and cites nothing. Other modes still use no network.
- Format: `[!evidence]` layout in `docs/format.md`. The Seedbed plugin ignores its content from 0.8.2; evidence is not a plugin feature.

## 0.3.0
- **Feynman is stricter:** a gap closes only when the answer explicitly explains that term (or gives a reason for that leap); otherwise up to 2 follow-up questions per gap, then the gap is open and the gate does not pass while any gap is open. The agent still never judges correctness. Examples are in `references/feynman.md`.
- **New modes:** retrieval (recall practice inside review, `[!retrieval]` callout), connect (asks whether other Seedbed notes relate; links only after a yes), digest (weekly summary, no file unless asked), premortem (`[!premortem]` callout, adds `premortem` to `gates`, no status change).
- Search by frontmatter (`type:`) so non-Seedbed notes are never opened or named.
- Moved to its own repository (history kept from the Seedbed repository's `skill/s1` branch). MIT license. Claude Code plugin marketplace manifest added.

## 0.2.0
- Devil's Advocate, Feynman and setup modes; `templates/concept.md`.

## 0.1.0
- First version: capture, socratic, review.
