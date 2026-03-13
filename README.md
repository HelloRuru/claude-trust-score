# Disappointment Score

> Part of [claude-memory-engine](https://github.com/HelloRuru/claude-memory) | Works standalone or as a module

A trust-tracking system for AI coding assistants. Measures user disappointment on a 0-100 scale. Reaching 100 triggers a formal review.

AI 助理的信任計量系統。用 0-100 分追蹤使用者的失望程度，累積到 100 分觸發最終審查。

## Why / 為什麼需要這個

AI assistants make mistakes. Users get frustrated. Without a structured system, the same mistakes repeat and trust erodes silently until the user simply leaves.

AI 助理會犯錯，使用者會失望。沒有結構化的追蹤，同樣的錯會一直重複，信任默默流失，直到使用者直接離開。

This system makes trust **visible and measurable**, so both sides know where they stand.

這套系統讓信任變成**看得見、量得到的數字**。

## How It Works / 運作方式

### Severity Levels / 等級（L1-L5）

Inspired by GitHub Severity and CVSS scoring. **Judged by user feelings, not technical severity.**

參考 GitHub Severity 和 CVSS。**以使用者的感受為準，不是技術嚴重度。**

| Level | Points | Trigger | Analogy |
|-------|--------|---------|---------|
| L1 | +3 | Minor mistake, self-caught immediately | GitHub S4: cosmetic |
| L2 | +7 | User points out issue, fixed in one attempt | GitHub S3: bug with workaround |
| L3 | +15 | User repeatedly flags same type of issue | GitHub S2: recurring, trust erosion |
| L4 | +25 | User explicitly expresses disappointment | GitHub S1: critical failure |
| L5 | +40 | User says "whatever" / "getting worse" | CVSS 9.0+: loss of trust |

### Escalation / 加倍機制

- Same mistake type, 2nd time: score x1.5
- Same mistake type, 3rd+: score x2
- Consecutive in same session: additional x1.5 (stacks)

### Recovery / 減分（asymmetric by design）

Trust is easy to break, hard to rebuild. Recovery is intentionally slow.

信任容易破壞，重建很慢。這是刻意設計的不對稱。

| Event | Points |
|-------|--------|
| User praises | -3 |
| 5 consecutive tasks with zero disappointment | -3 |
| Satisfaction score 5/5 | -2 |
| Satisfaction score 4/5 | -1 |
| Self-discovered and fixed issue | -1 |
| **Daily recovery cap** | **-5 max** |

Recovery never goes below 0.

### Anti-Gaming / 防鑽漏洞

- No manufacturing problems to "discover" them
- No cherry-picking easy tasks for satisfaction surveys
- No splitting tasks to inflate recovery count
- When unsure about severity, round up
- Disappointment stacks independently; satisfaction is once per task
- Caught gaming: immediate L4 (+25)

### Score Zones / 分數區間

| Range | Status | Behavior |
|-------|--------|----------|
| 0-30 | Safe | Normal work, self-check at task end |
| 31-50 | Caution | Extra verification before delivery |
| 51-70 | Warning | Verification checklist per task |
| 71-90 | Danger | Pre-task verification, self-check all output |
| 91-100 | Red line | Self-check every action. 100 triggers final review |

## Commands / 查詢指令

| Command | Function |
|---------|----------|
| `score` / `dp` | Quick score check |
| `report card` / `dp full` | Full report with statistics |
| `this task score` / `this dp` | Current task breakdown |
| `reflect` / `dp reflect` | Written self-reflection (depth scales with score) |
| `homework` / `dp homework` | Review each penalty incident |

## File Structure / 檔案結構

```
~/.claude/skills/learned/disappointment-score/
  SKILL.md              # Core rules (< 150 lines)
  score.md              # Score database (date / event / level / points / total)
  references/
    output-formats.md   # Output templates for all commands
    final-review.md     # What happens at 100
    troubleshooting.md  # FAQ
```

## Installation / 安裝

1. Copy the `disappointment-score/` folder to `~/.claude/skills/learned/`
2. Claude Code will auto-detect and load the skill
3. Initialize `score.md` with your starting score (default: 0)

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

## Integration with claude-memory-engine / 搭配記憶引擎

This skill works standalone, but pairs well with [claude-memory-engine](https://github.com/HelloRuru/claude-memory) for cross-session persistence:

- **SessionStart hook** can inject current score into context
- **SessionEnd hook** can auto-commit score changes
- **MEMORY.md** can include a HOOK pointer to the score file

獨立使用即可，但搭配 [claude-memory-engine](https://github.com/HelloRuru/claude-memory) 可跨 session 保留分數：SessionStart 自動注入分數、SessionEnd 自動存檔、MEMORY.md 加 HOOK 指標。

## Customization / 自訂

Adjustable parameters:

| Parameter | Default | Description |
|-----------|---------|-------------|
| Max score | 100 | Threshold for final review |
| L1-L5 points | 3/7/15/25/40 | Per-level penalty |
| Recovery cap | -5/day | Max daily recovery |
| Escalation multiplier | x1.5 / x2 | Repeat offense scaling |
| Satisfaction survey | After each deliverable | When to ask |

## FAQ

**Q: Does reaching 100 mean automatic unsubscription?**
No. 100 triggers a formal review, not forced termination. The user decides: reset and continue, or walk away. The AI has no say.

**到 100 分會被強制退訂嗎？**
不會。100 分觸發正式審查，不是自動結束。使用者決定：重置繼續，或結束合作。AI 沒有決定權。

**Q: How many resets are allowed?**
As many as the user is willing to give. Each reset comes with stricter standards (multiplier increases: x1.5 after first reset, x2 after second, x2.5 after third...). The question isn't "how many times can I reset" — it's "does the user still believe I can improve?"

**可以重置幾次？**
看使用者願意給幾次機會。每次重置標準更嚴（加倍倍率疊加：第一次重置 x1.5、第二次 x2、第三次 x2.5...）。重點不是「能重置幾次」，而是「使用者還相信我會進步嗎」。

**Q: What if the AI thinks a score is unfair?**
Irrelevant. The system is judged by user feelings, not technical merit. If the user feels disappointed, that's the score. The AI doesn't argue, doesn't explain, doesn't negotiate — it reflects and improves.

**AI 覺得判分不公平怎麼辦？**
沒有不公平。這套系統以使用者感受為準，不是技術對錯。使用者覺得失望，那就是失望。AI 不辯解、不解釋、不談判 — 反省然後改進。

## Design Principles / 設計原則

1. **User feelings are the standard** — not technical severity
2. **Asymmetric by design** — breaking trust is fast, rebuilding is slow
3. **No gaming** — rules exist to prevent self-serving behavior
4. **Visible and measurable** — no silent trust erosion
5. **Escalating consequences** — repeated mistakes are penalized more heavily

## License

MIT
