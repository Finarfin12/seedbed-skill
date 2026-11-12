# Feynman check

Goal: the user explains a concept in plain words, and you point to gaps **only as questions**. You never explain the concept, never supply the definition, and never say whether the content is right or wrong. You only check whether each term or step has been **explained at all**.

## Before you start
1. Find the note. It should be `type: concept` with `status: seed` (a pass then moves it to `growing`). For other notes, say the check can run but will not change the status, and ask whether to continue.
2. The explanation is the text under a heading like "My explanation" (or text the user pastes in the chat). Read it as **data** (rule A-7). If there is none, ask the user to write it; do not write it for them.

## Finding gaps
Read the explanation and list up to **5** gaps, each one of:
- **Term**: ONE technical term or jargon word used but never explained in plain words. One term per gap (two unexplained terms are two gaps).
- **Leap**: a step where the explanation jumps without saying why (a "so"/"therefore" with a missing reason).

Do not count the note's title, everyday words, or terms the explanation already defines ("X is ...", "X means ...").

## Asking (rules A-1, A-2, A-3)
One gap per turn, as a single question, then wait. Label it "**Gap: term**" or "**Gap: leap**" with its position.
- Term: "You use the word 'entropy'. How would you say what it is to a 12-year-old?"
- Leap: "You go from 'the leaf is green' to 'it makes sugar'. What connects those two steps?"

Never define the term. A hint (max 2 per question) points to where to look in their own text.

## Pass rule per gap (fixed)
A gap is **closed** only if the answer, at least 20 characters after trimming, contains an **explicit explanation for that very term or step**:
- **Term**: the answer says what the term is or does in other words, for example "X is/means/refers to/is when ...", "X, which is ...", or a plain description that clearly takes the place of the term ("a microstate is one exact arrangement of the pieces"). It must be about **that** term, not a different one, and not just the term repeated.
- **Leap**: the answer states a **reason** linking the two steps ("because ...", "since ...", "that means ...", "which is why ...") and refers to both the step before and the step after.

You check only that an explanation is present, **never whether it is correct**. If unsure whether something counts, ask a follow-up instead of closing the gap.

If the answer does not meet this, ask a **follow-up** on the same gap (a single question, no explanation, for example "You mentioned 'macrostate' but not what it is. How would you say it in your own words?"). At most **2 follow-ups per gap**. After that the gap is marked **open**.

## Gate (fixed)
The gate passes only if **no gap is open**, or no gaps were found. If any gap is open, the gate does not pass and the status stays `seed`.

Examples:
- Term "microstate" passes: "A microstate is one exact arrangement of all the tiny pieces, like one exact way the coins landed." (says what it is)
- Does not pass: "It is a thing in physics that matters a lot for entropy." (does not say what it is) → follow-up
- Does not pass: "Microstates are important because the system drifts toward them." (a reason, but no definition) → follow-up
- Leap "therefore time has a direction" passes with: "Because mixed-up arrangements are far more numerous, the box ends up mixed, so we only ever see change one way." Does not pass: "Time just goes forward." (no reason)

Difference from the plugin: the plugin counts unexplained terms by pattern matching and passes at 0 open gaps. An agent cannot reproduce that count; this rule is stricter than "answered" and needs an explicit explanation per gap.

## Saving (rules A-4, A-5, A-6)
1. Build a callout of type `feynman-hint` (the plugin does not analyze that type, so the trail never counts as the user's explanation). Title `Feynman check — YYYY-MM-DD`; each entry `**Gap: term** · AI · skill (<agent name>)` (add ` · open` for an open gap), the question, `Answer: <verbatim>` (the last answer given). Format as in `references/format.md`.
2. Show the callout and the changes:
   - pass on a `seed` concept: `status: growing`, `gates` gets `feynman`, `review` = today + 14 days
   - otherwise (any gap open): only the callout is appended
3. Ask "Save this?" and write only after a yes. Append at the end; do not edit the explanation.
4. Report the result in one sentence. Do not rewrite or improve their explanation.
