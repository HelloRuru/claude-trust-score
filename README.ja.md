<h1 align="center">Disappointment Score</h1>

<p align="center">
  <strong>AIコーディングアシスタントのための信頼追跡システム。</strong><br>
  ユーザーの失望度を0-100で計測。100に達すると正式レビューが発動。<br>
  読める <code>.md</code> ファイルだけで構成。
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-D4A5A5?style=flat-square" alt="MIT License">
  <img src="https://img.shields.io/badge/claude_code-skill-E8B4B8?style=flat-square" alt="Claude Code Skill">
  <img src="https://img.shields.io/badge/dependencies-zero-A8B5A0?style=flat-square" alt="Zero Dependencies">
  <img src="https://img.shields.io/badge/score-0%2F100-B8A9C9?style=flat-square" alt="Score">
</p>

<p align="center">
  <a href="README.md">English</a> &nbsp;|&nbsp; <a href="README.zh-TW.md">繁體中文</a> &nbsp;|&nbsp; <b>日本語</b>
</p>

---

## 課題

- AIアシスタントはミスをする -- ユーザーは不満を感じるが、構造的な追跡方法がない
- 計測しなければ同じミスが繰り返され、信頼は静かに失われる
- ユーザーは警告なく離れていく -- AIは自分がどれだけ危険だったか知らない
- 信頼回復にフレームワークがない -- 失敗後の修復が曖昧

---

## 解決策

Disappointment Scoreは、GitHub SeverityとCVSSスコアリングに着想を得た**5段階の重大度システム**で、信頼を可視化・計測可能にします。

### :brain: 重大度レベル（L1-L5）

**技術的な重大度ではなく、ユーザーの感情で判定。**

| レベル | 点数 | トリガー | 類似 |
| :----- | :--- | :------- | :--- |
| L1 | +3 | 軽微なミス、すぐ自分で気づいて修正 | GitHub S4：外観上の問題 |
| L2 | +7 | ユーザーが指摘、1回で修正 | GitHub S3：回避策のあるバグ |
| L3 | +15 | ユーザーが同タイプの問題を繰り返し指摘 | GitHub S2：再発、信頼の侵食 |
| L4 | +25 | ユーザーが明確に失望を表明 | GitHub S1：重大な失敗 |
| L5 | +40 | ユーザーが「もういい」「悪化してる」と発言 | CVSS 9.0+：信頼の崩壊 |

### :link: エスカレーション

- 同タイプのミス2回目：スコア x1.5
- 同タイプのミス3回目以降：スコア x2
- 同セッション内で連続：追加 x1.5（累積可）

### :recycle: 回復（意図的な非対称設計）

信頼を壊すのは簡単、再構築は遅い。回復速度は意図的に遅く設計されています。

| イベント | 点数 |
| :------- | :--- |
| ユーザーからの称賛 | -3 |
| 失望ゼロのタスク5回連続 | -3 |
| 満足度 5/5 | -2 |
| 満足度 4/5 | -1 |
| 自ら問題を発見し修正 | -1 |
| **1日の回復上限** | **最大 -5** |

回復で0未満にはなりません。

---

## :detective: 不正防止

- 問題を自作自演して「発見」しない
- 簡単なタスクを選んで満足度を稼がない
- タスクを分割して回復回数を水増ししない
- 重大度が不明な場合は高い方に判定
- 失望は独立累計、満足度はタスクごとに1回のみ
- 不正発覚：即座にL4（+25）

---

## :traffic_light: スコアゾーン

| 範囲 | ステータス | 行動 |
| :--- | :--------- | :--- |
| 0-30 | 安全 | 通常作業、タスク完了時にセルフチェック |
| 31-50 | 注意 | 納品前に追加検証 |
| 51-70 | 警告 | タスクごとに検証チェックリスト |
| 71-90 | 危険 | タスク前に検証、全出力をセルフチェック |
| 91-100 | レッドライン | 全アクションをセルフチェック。100で最終レビュー発動 |

---

## :speech_balloon: コマンド

| コマンド | 機能 |
| :------- | :--- |
| `score` / `dp` | クイックスコア確認 |
| `report card` / `dp full` | 統計付きフルレポート |
| `this task score` / `this dp` | 現タスクの内訳 |
| `reflect` / `dp reflect` | 書面による自己省察（深さはスコアに比例） |
| `homework` / `dp homework` | 各ペナルティ事案の振り返り |

---

## :package: インストール

**ステップ1** -- skillフォルダをコピー：

```bash
cp -r disappointment-score/ ~/.claude/skills/learned/
```

**ステップ2** -- Claude Codeが自動検出してskillを読み込みます。

**ステップ3** -- `score.md` を初期スコアで初期化：

```markdown
# Disappointment Score

**Current: 0 / 100**

| Date | Event | Level | Points | Total |
|------|-------|-------|--------|-------|
```

完了！

---

## :link: claude-memory-engineとの連携

> [claude-memory-engine](https://github.com/HelloRuru/claude-memory) の一部 | スタンドアロンでもモジュールとしても使用可能

単体で動作しますが、claude-memory-engineと組み合わせるとセッション間でスコアを保持できます：

- **SessionStart hook** -- 現在のスコアをcontextに注入
- **SessionEnd hook** -- スコア変更を自動commit
- **MEMORY.md** -- スコアファイルへのHOOKポインタを追加

---

## :open_file_folder: ファイル構成

```
~/.claude/skills/learned/disappointment-score/
  SKILL.md              # コアルール（150行未満）
  score.md              # スコアDB（日付 / イベント / レベル / 点数 / 累計）
  references/
    output-formats.md   # 全コマンドの出力テンプレート
    final-review.md     # 100点到達時の処理
    troubleshooting.md  # FAQ
```

---

## :wrench: カスタマイズ

| 変更したいこと | 変更場所 | デフォルト |
| :------------- | :------- | :--------- |
| 満点上限 | `SKILL.md` | 100 |
| L1-L5の点数 | `SKILL.md` | 3 / 7 / 15 / 25 / 40 |
| 1日の回復上限 | `SKILL.md` | -5/日 |
| エスカレーション倍率 | `SKILL.md` | x1.5 / x2 |
| 満足度調査のタイミング | `SKILL.md` | 各納品後 |

---

## :bulb: 設計思想

**なぜ技術的重大度ではなくユーザーの感情なのか？**
AIを使い続けるかどうかを決めるのはユーザーだから。技術的に正しいかは関係ない。ユーザーが無視された、軽く扱われた、苛立たされたと感じたら、それが失敗。ユーザーの体験が唯一の指標。

**なぜ回復がこんなに遅いのか？**
信頼は本質的に非対称。一瞬の不注意が数週間の努力を台無しにできる。遅い回復はこの現実を反映している -- 罰ではなく、信頼の本当の仕組み。

**100点に達するとどうなる？**
正式レビューが発動。強制終了ではない。ユーザーが決める：リセットして継続するか、終了するか。AIに決定権はない。リセットごとに基準が厳しくなる（倍率累積：x1.5、x2、x2.5...）。

**AIがスコアを不公平だと思ったら？**
関係ない。このシステムはユーザーの感情で判定する。AIは弁解しない、説明しない、交渉しない -- 省察して改善する。

---

## 要件

- [Claude Code](https://claude.com/claude-code) CLI
- 他の依存関係なし

## ライセンス

MIT -- 詳細は [LICENSE](LICENSE) を参照。

---

<p align="center">
  Made by <a href="https://ohruru.com">HelloRuru</a> -- 信頼は見えるべきもの、仮定されるべきものではない。
</p>
