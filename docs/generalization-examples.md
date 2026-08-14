---
md_class: operation
---

# AOTPの汎化例

AOTPは特定のAI製品や特定の開発プロジェクトに依存しない。共通して扱うのは、エージェント、役割、権限、状態、成果物、検証、引き継ぎである。

以下はAOTPを異なる作業へ適用したときの例である。

## 1. ソフトウェア開発

```text
人間
  ↓ 要求・優先度
Planner / Router
  ↓
Research → Design → Implementation → Review → Verification
```

- Taskに目的と完了条件を置く
- Researchで未知事項を外部化する
- Designで実装方針を固定する
- ImplementationはDesignとTaskを根拠に変更する
- Reviewは実装担当とは別のエージェントでもよい
- Test結果と残課題をTaskへ残す

Codex、Claude Code、Gemini CLIなど異なるruntimeを同じ工程に割り当てられる。

## 2. 調査・研究

```text
問い
 ↓
調査分割
 ├─ 文献調査
 ├─ 反例探索
 ├─ 実装・実験
 └─ 既存手法比較
 ↓
統合・批判的レビュー
 ↓
Research / Decision
```

調査担当ごとに読む資料と問いを限定し、最終エージェントは各調査結果だけを統合する。大量の資料を全エージェントへ重複投入しない。

## 3. 設計書・資料作成

```text
要求
 ↓
構成設計
 ↓
章・節ごとの生成
 ↓
事実確認
 ↓
全体整合レビュー
 ↓
成果物
```

文章生成と事実確認を別エージェントに分けられる。資料制約、文字数、参照可能な根拠、表現規則をMarkdownへ外部化し、成果物生成時の入力契約として扱う。

## 4. 大規模リファクタリング

```text
Epic
 ├─ architecture research
 ├─ dependency research
 ├─ migration design
 └─ child tasks
       ├─ implementation A
       ├─ implementation B
       └─ implementation C
             ↓
       independent review
```

一つの巨大な会話へ全作業を押し込まず、Epic、Research、Design、Taskへ分割する。独立した読み取り調査は並列化できるが、同じファイルへの並行編集は避ける。

## 5. 非コード作業

AOTPの対象はコードだけではない。

- 旅行計画
- 就職活動の調査・比較
- 定期レポート
- 文献管理
- データ分析
- 個人プロジェクトの企画

たとえば旅行計画なら、Researchを交通・宿泊・観光へ分割し、Decisionに採用した旅程と理由を残し、Statusに現在の予約状況を置ける。

## 6. 複数AI runtimeの協調

```text
Task
 ↓
Router
 ├─ Codex       : 実装・自律作業
 ├─ Claude Code : 読解・対話的設計
 ├─ Gemini CLI  : 調査・別視点
 └─ その他      : 専門作業
 ↓
共通Markdown
 ↓
Review / Verification
```

runtimeごとの独自機能はAdapter側へ閉じ込め、Task、Research、Design、Decisionなどの共有状態は可能な限りAOTPの共通形式で表現する。

## 汎化時の原則

AOTPを別用途へ適用するときも、次は変えない。

- 状態を会話だけに閉じない
- 一文書一責務を守る
- エージェントの権限と責務を明示する
- 必要な文脈だけを連鎖リンクで読む
- 成果物と検証根拠を接続する
- 人間が短時間で監査できる形を維持する

個別用途の違いはProject Profile、Skill、Agent、Workflowで吸収する。AOTP Coreそのものを用途ごとに分岐させない。
