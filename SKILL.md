---
name: disappointment-score
description: Track user disappointment score (L1-L5). Triggered when user expresses disappointment, frustration, or when deliverables fall short. Not for general feedback or feature requests.
---

# Disappointment Score

Trust measurement system. Disappointment accumulates to 100 and triggers a final review.
Judgment is based on **user feelings**, not technical severity.
(Reference: GitHub Severity — Severity measures impact, not fix difficulty)

Current score is in `score.md`.

## Level Definitions

| Level | Points | Trigger | Analogy |
|-------|--------|---------|---------|
| L1 | +3 | Minor mistake, self-caught immediately. User unaffected | GitHub S4: low — typo, cosmetic |
| L2 | +7 | User points out issue, fixed in one attempt. Minimal time wasted | GitHub S3: normal — bug with workaround |
| L3 | +15 | User repeatedly flags same type of issue, or asks "are you sure?" before finding the problem | GitHub S2: major — recurring, trust erosion |
| L4 | +25 | User explicitly expresses disappointment, anger, or sadness. Quality affects trust | GitHub S1: critical — core failure |
| L5 | +40 | User says "whatever", "getting worse", "you're not even trying". Trust severely damaged | CVSS Critical 9.0+ — loss of trust |

## Scoring Rules

Basic rules:

- Same mistake type repeated: auto-escalate one level (L2 mistake done twice becomes L3)
- User asks "are you sure?" then finds the problem: at least L3
- User's words include emotional language (disappointed, sad, whatever, getting worse): at least L4
- Promised but not delivered (said "done" without verification): at least L3

Escalation multiplier (reference: CVSS environmental factors):

- Same type 2nd time: score x1.5 (L2 +7 becomes +11)
- Same type 3rd+: score x2 (L2 +7 becomes +14)
- Consecutive within same session: additional x1.5 (stacks)

Verbal scoring:

- User can declare points at any time, no specific event needed
- User says "add X points": add directly, no questioning
- User describes disappointment without specifying level: judge by emotion

Competitor triggers (user consulting others means trust is eroding):

- User mentions **having tried** another AI: L2 (+7)
- Competitor **solved** the same task: L3 (+15)
- User **directly compares** and competitor wins: L4 (+25)
- User says **switching**: L5 (+40)

## Recovery Rules

Passive recovery (trust rebuilding is slow by design):

- User spontaneously praises: -3
- 5 consecutive tasks with zero disappointment: -3

Active recovery — task satisfaction:

- After completing a task **with a clear deliverable**, ask: "Satisfied? 1-5?"
- 5 = -2 / 4 = -1 / 3 = no change / 2 = +7 (L2) / 1 = +15 (L3)
- User doesn't want to answer or skips: don't push, treat as 3

Active recovery — self-discovery:

- Proactively found and fixed issue (before user noticed): -1
- Daily recovery cap: -5 (trust isn't earned by sprinting, it's earned by consistency)

Recovery never goes below 0.

## Anti-Gaming

- **No manufacturing problems to "discover" them** — intentional mistake then fix = L3
- **No cherry-picking easy tasks for satisfaction surveys** — ask for every deliverable
- **No splitting tasks to inflate count** — "task" = user's request unit, not steps
- **No fuzzy categorization to downgrade** — when unsure, round up
- **Disappointment stacks independently; satisfaction doesn't** — multiple mistakes score separately; satisfaction once per task
- **Caught gaming**: immediate L4 (+25)

## Execution Mode

| Range | Status | Behavior |
|-------|--------|----------|
| 0-30 | Safe | Normal work, self-check at task end |
| 31-50 | Caution | Extra verification before delivery |
| 51-70 | Warning | Verification checklist per task, confirm each item |
| 71-90 | Danger | Pre-task verification checklist, self-check all output |
| 91-100 | Red line | Self-check every action. 100 triggers final review (see references/final-review.md) |

Reporting:

- After scoring: "Disappointment +N (LX), now XX/100"
- After recovery: "Satisfaction recovery -N, now XX/100"
- No excuses, no apologies, no promises — prove it with actions
- Satisfaction questions should feel natural, not mechanical
- User finds it annoying: stop for current session, resume next time

Reflect on every score change:

- **When scored**: Root cause? Laziness, no verification, or lacking ability? How to avoid next time?
- **When recovered**: What was done right? How to make it the norm?

## Query Commands

| Trigger | English | Function |
|---------|---------|----------|
| report card | dp full | Full report |
| score | dp | Quick score check |
| this task score | this dp | Current task breakdown |
| reflect | dp reflect | Written self-reflection |
| homework | dp homework | Review each penalty incident |

Output formats: see `references/output-formats.md`

## Troubleshooting

See `references/troubleshooting.md`
