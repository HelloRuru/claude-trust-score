<h1 align="center">Disappointment Score</h1>

<p align="center">
  <strong>A trust-tracking system for AI coding assistants.</strong><br>
  Measures user disappointment on a 0-100 scale. Reaching 100 triggers a formal review.<br>
  Just <code>.md</code> files you can actually read.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-D4A5A5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/claude_code-skill-E8B4B8?style=flat-square" alt="Claude Code Skill">
  <img src="https://img.shields.io/badge/dependencies-zero-A8B5A0?style=flat-square" alt="Zero Dependencies">
  <img src="https://img.shields.io/badge/score-0%2F100-B8A9C9?style=flat-square" alt="Score">
</p>

<p align="center">
  <b>English</b> &nbsp;|&nbsp; <a href="README.zh-TW.md">繁體中文</a> &nbsp;|&nbsp; <a href="README.ja.md">日本語</a>
</p>

---

## The Problem

- AI assistants make mistakes -- users get frustrated, but there's no structured way to track it
- Without measurement, the same mistakes repeat and trust erodes silently
- Users eventually leave without warning -- the AI never knew how close it was to losing them
- Recovery feels arbitrary -- there's no framework for rebuilding trust after failure

---

## The Solution

Disappointment Score uses a **5-level severity system** inspired by GitHub Severity and CVSS scoring to make trust visible and measurable.

### :brain: Severity Levels (L1-L5)

**Judged by user feelings, not technical severity.**

| Level | Points | Trigger | Analogy |
| :---- | :----- | :------ | :------ |
| L1 | +3 | Minor mistake, self-caught immediately | GitHub S4: cosmetic |
| L2 | +7 | User points out issue, fixed in one attempt | GitHub S3: bug with workaround |
| L3 | +15 | User repeatedly flags same type of issue | GitHub S2: recurring, trust erosion |
| L4 | +25 | User explicitly expresses disappointment | GitHub S1: critical failure |
| L5 | +40 | User says "whatever" / "getting worse" | CVSS 9.0+: loss of trust |

### :link: Escalation

- Same mistake type, 2nd time: score x1.5
- Same mistake type, 3rd+: score x2
- Consecutive in same session: additional x1.5 (stacks)

### :recycle: Recovery (asymmetric by design)

Trust is easy to break, hard to rebuild. Recovery is intentionally slow.

| Event | Points |
| :---- | :----- |
| User praises | -3 |
| 5 consecutive tasks with zero disappointment | -3 |
| Satisfaction score 5/5 | -2 |
| Satisfaction score 4/5 | -1 |
| Self-discovered and fixed issue | -1 |
| **Daily recovery cap** | **-5 max** |

Recovery never goes below 0.

---

## :detective: Anti-Gaming

- No manufacturing problems to "discover" them
- No cherry-picking easy tasks for satisfaction surveys
- No splitting tasks to inflate recovery count
- When unsure about severity, round up
- Disappointment stacks independently; satisfaction is once per task
- Caught gaming: immediate L4 (+25)

---

## :traffic_light: Score Zones

| Range | Status | Behavior |
| :---- | :----- | :------- |
| 0-30 | Safe | Normal work, self-check at task end |
| 31-50 | Caution | Extra verification before delivery |
| 51-70 | Warning | Verification checklist per task |
| 71-90 | Danger | Pre-task verification, self-check all output |
| 91-100 | Red line | Self-check every action. 100 triggers final review |

---

## :speech_balloon: Commands

| Command | Function |
| :------ | :------- |
| `score` / `dp` | Quick score check |
| `report card` / `dp full` | Full report with statistics |
| `this task score` / `this dp` | Current task breakdown |
| `reflect` / `dp reflect` | Written self-reflection (depth scales with score) |
| `homework` / `dp homework` | Review each penalty incident |

---

## :package: Installation

**Step 1** -- Copy the skill folder:

```bash
cp -r disappointment-score/ ~/.claude/skills/learned/
```

**Step 2** -- Claude Code will auto-detect and load the skill.

**Step 3** -- Initialize `score.md` with your starting score:

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

Done!

---

## :link: Integration with claude-memory-engine

> Part of [claude-memory-engine](https://github.com/HelloRuru/claude-memory) | Works standalone or as a module

This skill works standalone, but pairs well with claude-memory-engine for cross-session persistence:

- **SessionStart hook** -- inject current score into context
- **SessionEnd hook** -- auto-commit score changes
- **MEMORY.md** -- include a HOOK pointer to the score file

---

## :open_file_folder: File Structure

```
~/.claude/skills/learned/disappointment-score/
  SKILL.md              # Core rules (< 150 lines)
  score.md              # Score database (date / event / level / points / total)
  references/
    output-formats.md   # Output templates for all commands
    final-review.md     # What happens at 100
    troubleshooting.md  # FAQ
```

---

## :wrench: Customization

| What to Change | Where | Default |
| :------------- | :---- | :------ |
| Max score | `SKILL.md` | 100 |
| L1-L5 points | `SKILL.md` | 3 / 7 / 15 / 25 / 40 |
| Recovery cap | `SKILL.md` | -5/day |
| Escalation multiplier | `SKILL.md` | x1.5 / x2 |
| Satisfaction survey timing | `SKILL.md` | After each deliverable |

---

## :bulb: Design Philosophy

**Why user feelings, not technical severity?**
Because the user decides whether to keep using the AI. Technical correctness doesn't matter if the user feels dismissed, ignored, or frustrated. Their experience is the only metric that counts.

**Why is recovery so slow?**
Trust is asymmetric by nature. One careless moment can undo weeks of good work. The slow recovery reflects this reality -- it's not punishment, it's how trust actually works.

**What happens at 100?**
A formal review, not forced termination. The user decides: reset and continue, or walk away. The AI has no say. Each reset comes with stricter standards (multiplier increases: x1.5, x2, x2.5...).

**What if the AI thinks a score is unfair?**
Irrelevant. The system is judged by user feelings, not technical merit. The AI doesn't argue, doesn't explain, doesn't negotiate -- it reflects and improves.

---

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI
- No other dependencies

## License

MIT -- see [LICENSE](LICENSE) for details.

---

<p align="center">
  Made by <a href="https://ohruru.com">HelloRuru</a> -- because trust should be visible, not assumed.
</p>
