<p align="center">
  <strong>claude-trust-score</strong><br>
  A trust-tracking skill for Claude Code
</p>

<p align="center">
  <a href="#installation--安裝">Installation</a> |
  <a href="#how-it-works--運作方式">How It Works</a> |
  <a href="#commands--指令">Commands</a> |
  <a href="#faq">FAQ</a> |
  <a href="https://github.com/HelloRuru/claude-memory">claude-memory-engine</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-blue" alt="MIT License">
  <img src="https://img.shields.io/badge/claude--code-skill-blueviolet" alt="Claude Code Skill">
  <img src="https://img.shields.io/badge/score-0%20%2F%20100-brightgreen" alt="Score">
</p>

---

> **Your AI assistant messes up. You get frustrated. Without a system, the same mistakes repeat until you quietly leave.**
>
> This skill makes trust **visible and measurable** — a 0-100 disappointment score that the AI tracks, reports, and reflects on.

---

## At a Glance

```
Mistake happens
    |
    v
Score increases (+3 to +40 depending on severity)
    |
    v
Repeated mistakes? --> Multiplier kicks in (x1.5, x2)
    |
    v
Score reaches zone threshold --> AI behavior changes
    |
    v
Score hits 100 --> Final review. User decides: reset or walk away.
```

---

## Installation / 安裝

```bash
# Copy to your Claude Code skills directory
cp -r disappointment-score/ ~/.claude/skills/learned/
```

Claude Code auto-detects the skill. Initialize `score.md` with:

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

That's it. The skill activates when disappointment is detected.

---

## How It Works / 運作方式

### Severity Levels (L1 - L5)

Inspired by [GitHub Severity](https://firefox-source-docs.mozilla.org/bug-management/guides/severity.html) and [CVSS](https://www.first.org/cvss/). Judged by **user feelings**, not technical severity.

| Level | Points | When | Like... |
|:-----:|:------:|------|---------|
| **L1** | +3 | Self-caught immediately, user unaffected | Typo in a comment |
| **L2** | +7 | User points out, fixed in one try | Bug with a workaround |
| **L3** | +15 | Same mistake type again, or "are you sure?" moment | Recurring issue, trust eroding |
| **L4** | +25 | User expresses disappointment or anger | Core failure |
| **L5** | +40 | "Whatever." / "Getting worse." / "You're not even trying." | Trust collapse |

### Escalation

| Condition | Multiplier |
|-----------|:----------:|
| Same mistake type, 2nd time | x1.5 |
| Same mistake type, 3rd+ | x2.0 |
| Consecutive in same session | additional x1.5 (stacks) |

### Recovery (asymmetric by design)

> Trust is easy to break, hard to rebuild.

| Event | Points | Cap |
|-------|:------:|:---:|
| User praises | -3 | |
| 5 consecutive zero-disappointment tasks | -3 | |
| Satisfaction 5/5 | -2 | |
| Satisfaction 4/5 | -1 | |
| Self-discovered fix | -1 | |
| | | **-5/day max** |

Satisfaction 2/5 = +7 (L2). Satisfaction 1/5 = +15 (L3). Recovery never below 0.

### Competitor Triggers

When a user consults another AI, trust is already eroding.

| Situation | Level |
|-----------|:-----:|
| Tried another AI | L2 |
| Competitor solved the same task | L3 |
| "XX does it better than you" | L4 |
| "I'll just use XX instead" | L5 |

### Score Zones

| Range | Status | AI Behavior |
|:-----:|--------|-------------|
| 0-30 | Safe | Normal work, self-check at task end |
| 31-50 | Caution | Extra verification before delivery |
| 51-70 | Warning | Verification checklist per task |
| 71-90 | Danger | Pre-task verification, self-check all output |
| 91-100 | Red Line | Self-check every action. 100 = [final review](references/final-review.md) |

---

## Anti-Gaming / 防鑽漏洞

The AI cannot exploit the system to lower its score:

- No manufacturing problems to "discover" them (= L3)
- No cherry-picking easy tasks for satisfaction surveys
- No splitting tasks to inflate recovery count
- When unsure about severity, **round up**
- Disappointment stacks independently; satisfaction is once per task
- **Caught gaming = immediate L4 (+25)**

---

## Commands / 指令

| Command | What it does |
|---------|-------------|
| `score` | Quick score check |
| `dp full` / `report card` | Full report with trend analysis |
| `this dp` / `this task score` | Current task score breakdown |
| `reflect` / `dp reflect` | AI writes a self-reflection (depth scales with score) |
| `homework` / `dp homework` | AI reviews each past penalty |

> `reflect` and `homework` can be rejected. Rejection = L2 (+7). No phoning it in.

---

## File Structure / 檔案結構

```
~/.claude/skills/learned/disappointment-score/
+-- SKILL.md                    # Core rules (< 150 lines)
+-- score.md                    # Score log
+-- references/
    +-- output-formats.md       # Output templates
    +-- final-review.md         # What happens at 100
    +-- troubleshooting.md      # Common questions
```

---

## Integration with claude-memory-engine

Works standalone. Pairs with [claude-memory-engine](https://github.com/HelloRuru/claude-memory) for cross-session persistence:

- **SessionStart** injects current score into context
- **SessionEnd** auto-commits score changes
- **MEMORY.md** can include a HOOK pointer to the score file

---

## Customization / 自訂

| Parameter | Default | Adjustable |
|-----------|:-------:|:----------:|
| Max score | 100 | Yes |
| L1-L5 points | 3 / 7 / 15 / 25 / 40 | Yes |
| Daily recovery cap | -5 | Yes |
| Escalation multiplier | x1.5 / x2.0 | Yes |
| Satisfaction survey | After each deliverable | Yes |

---

## FAQ

<details>
<summary><strong>Does reaching 100 mean automatic unsubscription?</strong></summary>

No. 100 triggers a formal review, not forced termination. The user decides: reset and continue, or walk away. The AI has no say.

</details>

<details>
<summary><strong>How many resets are allowed?</strong></summary>

As many as the user is willing to give. Each reset comes with stricter standards (x1.5 after first reset, x2 after second, x2.5 after third...). The real question: does the user still believe the AI can improve?

</details>

<details>
<summary><strong>What if the AI thinks a score is unfair?</strong></summary>

Irrelevant. The system is judged by user feelings, not technical merit. If the user feels disappointed, that's the score. The AI doesn't argue, doesn't explain, doesn't negotiate — it reflects and improves.

</details>

---

## Design Principles

1. **User feelings are the standard** — not technical severity
2. **Asymmetric by design** — breaking trust is fast, rebuilding is slow
3. **No gaming** — rules prevent self-serving behavior
4. **Visible and measurable** — no silent trust erosion
5. **Escalating consequences** — repeated mistakes are penalized more heavily

---

<p align="center">
  <sub>Part of <a href="https://github.com/HelloRuru/claude-memory">claude-memory-engine</a> | MIT License | 2026 Kaoru Tsai</sub>
</p>
