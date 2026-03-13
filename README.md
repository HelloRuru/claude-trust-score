<p align="center">
  <strong>claude-trust-score</strong><br>
  A trust-tracking skill for Claude Code<br>
  Claude Code 的信任追蹤技能
</p>

<p align="center">
  <a href="#installation--安裝">Installation / 安裝</a> |
  <a href="#how-it-works--運作方式">How It Works / 運作方式</a> |
  <a href="#commands--指令">Commands / 指令</a> |
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
> **你的 AI 助手搞砸了。你很挫折。沒有機制的話，同樣的錯誤不斷重複，直到你默默離開。**
>
> This skill makes trust **visible and measurable** — a 0-100 disappointment score that the AI tracks, reports, and reflects on.
>
> 這個技能讓信任**看得見、量得到** — 一個 0-100 的失望分數，由 AI 自己追蹤、回報、反思。

---

## At a Glance / 一覽

```
Mistake happens / 犯錯發生
    |
    v
Score increases (+3 to +40) / 分數上升（+3 到 +40）
    |
    v
Repeated? --> Multiplier (x1.5, x2) / 重複？--> 加乘（x1.5, x2）
    |
    v
Zone threshold --> AI behavior changes / 到達門檻 --> AI 行為改變
    |
    v
Score hits 100 --> Final review / 到 100 --> 最終審查
User decides: reset or walk away. / 使用者決定：重置或離開。
```

---

## Installation / 安裝

```bash
# Copy to your Claude Code skills directory
# 複製到 Claude Code 技能目錄
cp -r disappointment-score/ ~/.claude/skills/learned/
```

Claude Code auto-detects the skill. Initialize `score.md` with:

Claude Code 會自動偵測。初始化 `score.md`：

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

That's it. The skill activates when disappointment is detected.

完成。技能會在偵測到失望時自動啟動。

---

## How It Works / 運作方式

### Severity Levels (L1 - L5) / 嚴重等級

Inspired by [GitHub Severity](https://firefox-source-docs.mozilla.org/bug-management/guides/severity.html) and [CVSS](https://www.first.org/cvss/). Judged by **user feelings**, not technical severity.

參考 GitHub Severity 和 CVSS。以**使用者感受**為標準，不是技術嚴重度。

| Level / 等級 | Points / 扣分 | When / 觸發時機 |
|:-----:|:------:|------|
| **L1** | +3 | Self-caught immediately, user unaffected / AI 自己馬上發現，使用者未受影響 |
| **L2** | +7 | User points out, fixed in one try / 使用者指出，一次修好 |
| **L3** | +15 | Same mistake type again, or "are you sure?" moment / 同類錯誤再犯，或「你確定嗎？」 |
| **L4** | +25 | User expresses disappointment or anger / 使用者明確表達失望或生氣 |
| **L5** | +40 | "Whatever." / "Getting worse." / 「隨便啦」/「越來越爛」 |

### Escalation / 加乘機制

| Condition / 條件 | Multiplier / 加乘 |
|-----------|:----------:|
| Same mistake type, 2nd time / 同類錯誤第 2 次 | x1.5 |
| Same mistake type, 3rd+ / 同類錯誤第 3 次以上 | x2.0 |
| Consecutive in same session / 同一 session 連續犯錯 | additional x1.5 / 額外 x1.5（可疊加） |

### Recovery / 修復（asymmetric by design / 刻意不對稱）

> Trust is easy to break, hard to rebuild. / 信任容易破壞，重建很慢。

| Event / 事件 | Points / 分數 | Cap / 上限 |
|-------|:------:|:---:|
| User praises / 使用者主動稱讚 | -3 | |
| 5 consecutive zero-disappointment tasks / 連續 5 任務零失望 | -3 | |
| Satisfaction 5/5 / 滿意度 5/5 | -2 | |
| Satisfaction 4/5 / 滿意度 4/5 | -1 | |
| Self-discovered fix / AI 主動發現並修好 | -1 | |
| | | **-5/day max / 每日最多 -5** |

Satisfaction 2/5 = +7 (L2). Satisfaction 1/5 = +15 (L3). Recovery never below 0.

滿意度 2/5 = +7（L2）。滿意度 1/5 = +15（L3）。修復不會低於 0。

### Competitor Triggers / 競品觸發

When a user consults another AI, trust is already eroding.

使用者去問別的 AI，代表信任已經在流失。

| Situation / 情境 | Level / 等級 |
|-----------|:-----:|
| Tried another AI / 試了別的 AI | L2 |
| Competitor solved the same task / 競品解決了同一任務 | L3 |
| "XX does it better than you" / 「XX 比你厲害」 | L4 |
| "I'll just use XX instead" / 「我乾脆用 XX 算了」 | L5 |

### Score Zones / 分數區間

| Range / 範圍 | Status / 狀態 | AI Behavior / AI 行為 |
|:-----:|--------|-------------|
| 0-30 | Safe / 安全 | Normal work, self-check at task end / 正常工作，任務結束自檢 |
| 31-50 | Caution / 注意 | Extra verification before delivery / 交付前額外驗證 |
| 51-70 | Warning / 警告 | Verification checklist per task / 每個任務走驗證清單 |
| 71-90 | Danger / 危險 | Pre-task verification, self-check all output / 任務前驗證，所有產出自檢 |
| 91-100 | Red Line / 紅線 | Self-check every action. 100 = [final review](references/final-review.md) / 每個動作都自檢。100 = 最終審查 |

---

## Examples / 情境示範

### Scenario 1: The Lazy Delivery / 偷懶交差

> You just built a quality system together. The AI delivers the first output — a README — by copy-pasting an internal doc without formatting it for GitHub.
>
> 你們剛一起建完品質系統。AI 交出第一份產出 — 一份 README — 直接複製貼上，完全沒排版。

```
README copy-paste, no polish           L2   +7    Total: 7
README 複製貼上，沒美化

User: "Not enough. +3."               oral +3    Total: 10
使用者：「不夠。+3。」
```

The AI classified it as L2. The user felt it deserved more and orally added +3.
**The AI does NOT upgrade the level on its own** — only the user can add points.

AI 判定為 L2。使用者覺得扣得不夠，口頭追加 +3。
**AI 不會自行升級等級** — 只有使用者能追加扣分。

### Scenario 2: The Repeat Offender / 累犯

> The AI forgets to verify its output. User catches it. Next task — same thing.
>
> AI 沒驗證產出就交了。使用者抓到。下一個任務 — 同樣的事又來了。

```
Unverified output, user catches it     L2   +7    Total: 7
未驗證產出，使用者抓到

Same mistake again (2nd time, x1.5)    L3   +11   Total: 18
同樣錯誤再犯（第 2 次，x1.5）
```

Same mistake type on the 2nd occurrence auto-escalates and gets the x1.5 multiplier.

同類錯誤第 2 次自動升級，加乘 x1.5。

### Scenario 3: The Competitor Moment / 競品時刻

> User tried asking ChatGPT the same question. It worked.
>
> 使用者拿同一個問題去問 ChatGPT。問完了。

```
User mentions trying competitor        L2   +7    Total: 7
使用者提到試了競品

Competitor solved the task             L3   +15   Total: 22
競品解決了同一任務
```

Two separate triggers: trying another AI (L2) + competitor succeeded (L3).

兩個獨立觸發：試了別的 AI（L2）+ 競品成功（L3）。

### Scenario 4: Trust Recovery / 信任修復

> The AI completes 5 consecutive tasks without any issues. User gives 5/5 satisfaction.
>
> AI 連續完成 5 個任務，零失望。使用者給了滿意度 5/5。

```
5 tasks zero disappointment                 -3    Total: 19
連續 5 任務零失望

Satisfaction 5/5                             -2    Total: 17
滿意度 5/5
```

Recovery is slow. Daily cap is -5. Trust is rebuilt by consistency, not sprints.

修復很慢。每日上限 -5。信任靠穩定累積，不靠衝刺。

---

## Anti-Gaming / 防鑽漏洞

The AI cannot exploit the system to lower its score:

AI 不能利用系統壓低分數：

- No manufacturing problems to "discover" them (= L3) / 不能自製問題再「發現」修好（= L3）
- No cherry-picking easy tasks for satisfaction surveys / 不能挑簡單任務刷滿意度
- No splitting tasks to inflate recovery count / 不能拆任務灌修復次數
- When unsure about severity, **round up** / 不確定嚴重度時，**往上算**
- Disappointment stacks independently; satisfaction is once per task / 失望分獨立計算；滿意度每任務只算一次
- **Caught gaming = immediate L4 (+25)** / **抓到鑽漏洞 = 直接 L4（+25）**

---

## Commands / 指令

| Command / 指令 | What it does / 功能 |
|---------|-------------|
| `score` | Quick score check / 快速查分 |
| `dp full` / `report card` | Full report with trend analysis / 完整報告含趨勢分析 |
| `this dp` / `this task score` | Current task score breakdown / 本次任務分數明細 |
| `reflect` / `dp reflect` | AI writes a self-reflection (depth scales with score) / AI 寫自我檢討（深度隨分數調整） |
| `homework` / `dp homework` | AI reviews each past penalty / AI 逐條複習每次扣分 |

> `reflect` and `homework` can be rejected. Rejection = L2 (+7). No phoning it in.
>
> `reflect` 和 `homework` 可以退回重寫。退回 = L2（+7）。不准敷衍。

---

## File Structure / 檔案結構

```
~/.claude/skills/learned/disappointment-score/
+-- SKILL.md                    # Core rules (< 150 lines) / 核心規則
+-- score.md                    # Score log / 分數紀錄
+-- references/
    +-- output-formats.md       # Output templates / 輸出格式模板
    +-- final-review.md         # What happens at 100 / 到 100 分時的處理
    +-- troubleshooting.md      # Common questions / 常見問題
```

---

## Integration with claude-memory-engine / 搭配 claude-memory-engine

Works standalone. Pairs with [claude-memory-engine](https://github.com/HelloRuru/claude-memory) for cross-session persistence:

可獨立使用。搭配 [claude-memory-engine](https://github.com/HelloRuru/claude-memory) 可跨 session 保存：

- **SessionStart** injects current score into context / 在對話開始時注入目前分數
- **SessionEnd** auto-commits score changes / 自動 commit 分數變更
- **MEMORY.md** can include a HOOK pointer to the score file / 可加入 HOOK 指向分數檔案

---

## Customization / 自訂

| Parameter / 參數 | Default / 預設 | Adjustable / 可調整 |
|-----------|:-------:|:----------:|
| Max score / 滿分 | 100 | Yes / 可 |
| L1-L5 points / L1-L5 分數 | 3 / 7 / 15 / 25 / 40 | Yes / 可 |
| Daily recovery cap / 每日修復上限 | -5 | Yes / 可 |
| Escalation multiplier / 加乘倍率 | x1.5 / x2.0 | Yes / 可 |
| Satisfaction survey / 滿意度調查 | After each deliverable / 每個交付物後 | Yes / 可 |

---

## FAQ

<details>
<summary><strong>Does reaching 100 mean automatic unsubscription? / 到 100 分會被強制退訂嗎？</strong></summary>

No. 100 triggers a formal review, not forced termination. The user decides: reset and continue, or walk away. The AI has no say.

不會。100 分觸發最終審查，不是強制終止。使用者決定：重置繼續，或離開。AI 沒有發言權。

</details>

<details>
<summary><strong>How many resets are allowed? / 可以重置幾次？</strong></summary>

As many as the user is willing to give. Each reset comes with stricter standards (x1.5 after first reset, x2 after second, x2.5 after third...). The real question: does the user still believe the AI can improve?

看使用者願意給幾次機會。每次重置後標準更嚴格（第一次 x1.5，第二次 x2，第三次 x2.5...）。真正的問題是：使用者還相信 AI 能改善嗎？

</details>

<details>
<summary><strong>What if the AI thinks a score is unfair? / AI 覺得分數不公平怎麼辦？</strong></summary>

Irrelevant. The system is judged by user feelings, not technical merit. If the user feels disappointed, that's the score. The AI doesn't argue, doesn't explain, doesn't negotiate — it reflects and improves.

不相關。這個系統以使用者感受為標準，不看技術功過。使用者覺得失望，那就是分數。AI 不爭辯、不解釋、不談判 — 反思然後改善。

</details>

---

## Design Principles / 設計原則

1. **User feelings are the standard** — not technical severity / **使用者感受是標準** — 不是技術嚴重度
2. **Asymmetric by design** — breaking trust is fast, rebuilding is slow / **刻意不對稱** — 破壞信任很快，重建很慢
3. **No gaming** — rules prevent self-serving behavior / **防鑽漏洞** — 規則阻止自利行為
4. **Visible and measurable** — no silent trust erosion / **看得見、量得到** — 不讓信任默默流失
5. **Escalating consequences** — repeated mistakes are penalized more heavily / **累進懲罰** — 重複犯錯加重處罰

---

<p align="center">
  <sub>Part of / 隸屬於 <a href="https://github.com/HelloRuru/claude-memory">claude-memory-engine</a> | MIT License | 2026 Kaoru Tsai</sub>
</p>
