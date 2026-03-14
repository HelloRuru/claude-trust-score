<h1 align="center">Disappointment Score</h1>

<p align="center">
  <strong>AI 助理的信任計量系統。</strong><br>
  用 0-100 分追蹤使用者的失望程度，累積到 100 分觸發最終審查。<br>
  只有看得懂的 <code>.md</code> 檔案。
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-D4A5A5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/claude_code-skill-E8B4B8?style=flat-square" alt="Claude Code Skill">
  <img src="https://img.shields.io/badge/dependencies-zero-A8B5A0?style=flat-square" alt="Zero Dependencies">
  <img src="https://img.shields.io/badge/score-0%2F100-B8A9C9?style=flat-square" alt="Score">
</p>

<p align="center">
  <a href="README.md">English</a> &nbsp;|&nbsp; <b>繁體中文</b> &nbsp;|&nbsp; <a href="README.ja.md">日本語</a>
</p>

---

## 痛點

- AI 助理會犯錯 -- 使用者會失望，但沒有結構化的追蹤方式
- 沒有量化，同樣的錯一直重複，信任默默流失
- 使用者最後直接離開 -- AI 永遠不知道自己離被放棄有多近
- 重建信任沒有框架 -- 犯錯之後的修復感覺很隨意

---

## 解決方案

Disappointment Score 用一套 **5 級嚴重度系統**（參考 GitHub Severity 和 CVSS）讓信任變成看得見、量得到的數字。

### :brain: 嚴重度等級（L1-L5）

**以使用者的感受為準，不是技術嚴重度。**

| 等級 | 分數 | 觸發條件 | 類比 |
| :--- | :--- | :------- | :--- |
| L1 | +3 | 小錯誤，自己馬上發現修好 | GitHub S4：外觀瑕疵 |
| L2 | +7 | 使用者指出問題，一次修好 | GitHub S3：有替代方案的 bug |
| L3 | +15 | 使用者反覆指出同類型問題 | GitHub S2：反覆出現，信任侵蝕 |
| L4 | +25 | 使用者明確表達失望 | GitHub S1：重大失敗 |
| L5 | +40 | 使用者說「隨便」/「越來越爛」 | CVSS 9.0+：信任崩盤 |

### :link: 加倍機制

- 同類型錯誤第 2 次：分數 x1.5
- 同類型錯誤第 3 次以上：分數 x2
- 同一個 session 連續犯錯：額外 x1.5（可疊加）

### :recycle: 減分（刻意不對稱）

信任容易破壞，重建很慢。減分速度是故意設計得慢的。

| 事件 | 分數 |
| :--- | :--- |
| 使用者給予稱讚 | -3 |
| 連續 5 個任務零失望 | -3 |
| 滿意度 5/5 | -2 |
| 滿意度 4/5 | -1 |
| 自己發現問題並修好 | -1 |
| **每日減分上限** | **最多 -5** |

減分不會低於 0。

---

## :detective: 防鑽漏洞

- 不能自己製造問題再「發現」
- 不能挑簡單任務來衝滿意度
- 不能把任務拆小來灌減分次數
- 不確定嚴重度時，往上算
- 失望分數各自獨立累計；滿意度每個任務只算一次
- 被抓到鑽漏洞：直接 L4（+25）

---

## :traffic_light: 分數區間

| 區間 | 狀態 | 行為 |
| :--- | :--- | :--- |
| 0-30 | 安全 | 正常工作，任務結束自檢 |
| 31-50 | 注意 | 交付前額外驗證 |
| 51-70 | 警告 | 每個任務跑驗證清單 |
| 71-90 | 危險 | 任務前驗證，所有輸出自檢 |
| 91-100 | 紅線 | 每個動作都自檢。100 分觸發最終審查 |

---

## :speech_balloon: 查詢指令

| 指令 | 功能 |
| :--- | :--- |
| `score` / `dp` | 快速查分 |
| `report card` / `dp full` | 完整報告（含統計） |
| `this task score` / `this dp` | 目前任務扣分明細 |
| `reflect` / `dp reflect` | 書面反省（深度隨分數增加） |
| `homework` / `dp homework` | 逐條檢討每個扣分事件 |

---

## :package: 安裝

**第 1 步** -- 複製 skill 資料夾：

```bash
cp -r disappointment-score/ ~/.claude/skills/learned/
```

**第 2 步** -- Claude Code 會自動偵測並載入 skill。

**第 3 步** -- 用起始分數初始化 `score.md`：

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

完成！

---

## :link: 搭配記憶引擎

> 屬於 [claude-memory-engine](https://github.com/HelloRuru/claude-memory) 的一部分 | 可獨立使用，也可作為模組

獨立使用即可，搭配 claude-memory-engine 可跨 session 保留分數：

- **SessionStart hook** -- 自動注入目前分數到 context
- **SessionEnd hook** -- 自動 commit 分數變更
- **MEMORY.md** -- 加 HOOK 指標指向分數檔案

---

## :open_file_folder: 檔案結構

```
~/.claude/skills/learned/disappointment-score/
  SKILL.md              # 核心規則（< 150 行）
  score.md              # 分數資料庫（日期 / 事件 / 等級 / 分數 / 累計）
  references/
    output-formats.md   # 所有指令的輸出模板
    final-review.md     # 100 分會怎樣
    troubleshooting.md  # 常見問題
```

---

## :wrench: 自訂

| 想改什麼 | 去哪裡改 | 預設值 |
| :------- | :------- | :----- |
| 滿分上限 | `SKILL.md` | 100 |
| L1-L5 分數 | `SKILL.md` | 3 / 7 / 15 / 25 / 40 |
| 每日減分上限 | `SKILL.md` | -5/天 |
| 加倍倍率 | `SKILL.md` | x1.5 / x2 |
| 滿意度調查時機 | `SKILL.md` | 每次交付後 |

---

## :bulb: 設計理念

**為什麼用使用者感受，不用技術嚴重度？**
因為決定要不要繼續用 AI 的人是使用者。技術上對不對不重要，使用者覺得被忽視、被敷衍、被惹毛了，那就是失敗。使用者的體驗是唯一的標準。

**為什麼減分這麼慢？**
信任天生不對稱。一個粗心的瞬間可以毀掉幾週的努力。慢速減分反映的是信任的本質 -- 這不是懲罰，這是信任真正的運作方式。

**到 100 分會怎樣？**
觸發正式審查，不是強制結束。使用者決定：重置繼續，或結束合作。AI 沒有決定權。每次重置標準更嚴（加倍倍率疊加：x1.5、x2、x2.5...）。

**AI 覺得判分不公平怎麼辦？**
沒有不公平。這套系統以使用者感受為準，不是技術對錯。AI 不辯解、不解釋、不談判 -- 反省然後改進。

---

## 需求

- [Claude Code](https://claude.com/claude-code) CLI
- 不需要其他依賴

## 授權

MIT -- 詳見 [LICENSE](LICENSE)。

---

<p align="center">
  Made by <a href="https://ohruru.com">HelloRuru</a> -- 信任應該被看見，不該被假設。
</p>
