---
md_class: status
---

# AOTP 1.0の課題

AOTP 1.0は、思想、文書モデル、導入契約を正本化した段階である。以下は今後の実装・検証が必要な主要課題である。

## 1. Loop Engineering的な実行基盤

現状のAOTPは、Markdownを中心にエージェントの行動を設計するが、作業ループそのものを機械的に制御する実行基盤は弱い。

今後はHookやイベントを利用し、作業の節目で自動的に検査・記録・再委譲できる構造を検討する。

例:

```text
Task開始
 ↓ hook
必要文書・権限・agentを決定
 ↓
実装
 ↓ hook
テスト・diff・文書更新を確認
 ↓
Review
 ↓ hook
未解決findingがあれば再実装
 ↓
完了条件成立までloop
```

検討対象:

- pre-task / post-task hook
- pre-edit / post-edit hook
- test完了時hook
- review finding発生時の再委譲
- commit / PR前のgate
- 長時間agentのcheckpoint
- 失敗回数や再試行上限
- 人間承認が必要な停止点

Loopを長く回すこと自体を目的にせず、終了条件、予算、権限、失敗時の停止条件を必ず持たせる。

## 2. 課題単位のAgent・モデル・コスト管理

現在は「どの作業を誰へ任せるか」を文書で表現できるが、課題ごとの計算資源を統一的に管理する仕組みは未確立である。

Taskまたはsubtaskごとに、少なくとも次を管理できるようにしたい。

- runtime: Codex / Claude Code / Gemini CLI / その他
- agent role
- model
- reasoning effort
- 実行回数
- 並列数
- token / credit / monetary cost
- 実行時間
- retry回数
- escalation履歴
- review担当

例:

```yaml
execution:
  runtime: codex
  agent: standard_worker
  model_class: standard
  reasoning: high
  budget:
    max_runs: 2
    max_cost: medium
  review:
    required: true
    independent_runtime: true
```

具体的なprovider固有model IDをTaskへ埋め込みすぎず、`fast` / `standard` / `deep` のような論理クラスと実際のモデル割当を分離することも検討する。

## 3. コスト最適化と品質の両立

単純に安価なモデルへ寄せるだけでは、失敗、再実行、regressionによって総コストが増える。

今後は以下を記録し、ルーティング改善へ使えるようにする。

- task種別ごとの成功率
- 初回完了率
- review finding数
- 手戻り回数
- agentごとの所要時間
- agentごとの利用量
- 高価モデルへのescalation理由

最適化対象はモデル単価ではなく、完了までの総資源と品質である。

## 4. 文書制約の強化

AOTPはMarkdownを実行状態として扱うため、自由記述だけでは規模拡大時に曖昧さが増える。

強化候補:

- typeごとの必須frontmatter
- schema validation
- relationの型制約
- status transition制約
- Task完了時の必須verification evidence
- Design承認前の必須review
- 文書サイズ・責務の上限
- 関連資料の許可リスト / 禁止リスト
- source / evidenceの明示
- 古い文書の検出
- 実装と文書の不一致検出

「Markdownなら何でもよい」状態にはしない。自然言語を使いながら、機械的に検証できる制約を増やす。

## 5. 資料・コンテキスト制約

各エージェントへ大量の文書を渡すと、省資源化の原則が崩れる。

Taskごとに次を明示できる仕組みが必要である。

- must_read
- may_read
- must_not_read
- source_of_truth
- max_context_scope
- output destination

例:

```yaml
context:
  must_read:
    - docs/designs/auth.md
    - docs/features/auth.md
  may_read:
    - docs/research/
  must_not_read:
    - unrelated historical tasks
  source_of_truth:
    - docs/features/auth.md
```

これにより「関係ありそうだからリポジトリ全部読む」という探索を抑え、誤った古い文書を参照するリスクも下げる。

## 6. 複数runtime間の共通Adapter

Codex、Claude Code、Gemini CLIなどは、subagent、Skill、Hook、permission、session、structured outputの仕様が異なる。

AOTP側では共通概念を定義し、provider固有仕様はAdapterへ閉じ込めたい。

```text
AOTP Task / Agent / Skill
          ↓
      Runtime Adapter
   ┌──────┼──────┐
 Codex  Claude  Gemini
```

Adapterが吸収する候補:

- prompt invocation
- model指定
- permissions
- sandbox
- hooks
- subagents
- structured output
- usage metrics
- session continuation

## 7. 自動導入と更新

現在はinstallerの契約があるが、汎用CLIは未実装である。

目標:

```text
AOTP repository URL
 ↓
対象AIが正本を読む
 ↓
inspect
 ↓
plan
 ↓
apply
 ↓
validate
```

既存プロジェクトを破壊せず、AOTP本体の更新とProject固有設定を分離してアップグレードできる必要がある。

## 8. 観測可能性と監査

複数agentが動くほど、「誰が、どのモデルで、何を読み、何を変更し、何を検証したか」が重要になる。

将来的にはTaskから次を追跡できる状態を目指す。

```text
Task
 ├─ execution 1: Claude / research
 ├─ execution 2: Codex / implementation
 ├─ execution 3: Gemini / verification
 └─ execution 4: Codex / review
```

人間がAIの内部思考を読むことを前提にせず、入力、行動、差分、成果、検証結果、コストを外部状態として監査できる形にする。

## 優先度

当面の優先順位は次とする。

1. 文書schemaと資料制約の強化
2. Task単位のAgent / runtime / cost記録
3. Runtime Adapterの共通化
4. Hookを利用したLoop Engineering的実行
5. installer CLIによる自動導入・更新
6. 実績データを用いた自動routing最適化

AOTP 1.0ではこれらを未完成の課題として明示し、実装済みであるかのように扱わない。
