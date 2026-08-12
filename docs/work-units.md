---
md_class: operation
aotp_version: 2
---

# AOTPの作業単位

AOTPでは、原則として **Taskを実行・記録・検証の基本単位** とする。

Taskより大きい要求はEpicとして束ね、Taskより小さい手順はTask内部のcheckpointや手順として扱う。階層を深くしすぎると、状態管理と引き継ぎのコストが増えるため、基本構造は単純に保つ。

```text
Epic
 ├─ Task
 ├─ Task
 └─ Task

Task
 ├─ checkpoint
 ├─ verification
 └─ result
```

## Task

TaskはAOTPにおける基本作業単位である。

Taskは、少なくとも次を持つ。

- 目的
- 完了条件
- 関連するDesign / Research / Decision / Status
- 担当Agentまたは人間
- 必要なruntime / model / role
- 実行結果
- 検証証拠
- 必要に応じてcost、retry、review記録

一つのTaskは、可能な限り一つの明確な成果または判断に対応させる。

Task内部で必要な小作業は、原則として新しい階層を増やさずcheckpointや手順として表現する。独立した担当、独立した検証、別のAgentへの委任、個別の失敗管理が必要になった時点で、別Taskへ分離する。

## Epic

Epicは、複数Taskを束ねる上位の目的・依頼・成果単位である。

特に次のような場合に使う。

- 他者から任された大きな依頼
- 一つの機能群やプロジェクト目標
- 複数の設計・実装・検証を含む作業
- 数日以上または複数Agentにまたがる仕事

Epicそのものを一体の実装単位にはしない。Epicは目的、制約、全体完了条件、Task間の関係を保持し、実際の処理は子Taskへ分解する。

```text
Epic: 認証方式の刷新

├─ Task: 現行認証の調査
├─ Task: 新方式の設計
├─ Task: backend実装
├─ Task: frontend対応
├─ Task: migration
└─ Task: 統合テスト
```

Epicは必ずしも外部依頼に限定しないが、「人間が大きな目的として管理したい粒度」と考えるとよい。

## 横断Task

すべての検証や保守を各Taskに重複して持たせる必要はない。

大規模テスト、長時間テスト、外部サービスを使うテスト、高コストなレビュー、定期監査などは、独立した **横断Task** として実行できる。

例えば、変更Taskごとに数十分のE2Eを実行するより、一定数のTaskが完了した後に統合テストTaskを一度実行する方が合理的な場合がある。

```text
Task A: 機能実装
Task B: バグ修正
Task C: UI変更
        ↓
Task T: 統合E2Eテスト
        ↓
Task R: 必要なら修正・再検証
```

横断Taskの例:

- 統合テスト
- 大規模E2E
- performance test
- security scan
- accessibility audit
- documentation consistency check
- dependency audit
- release review
- 定期的なAIレビュー

## Taskごとの検証と横断検証

検証は二層に分ける。

### Task-local verification

各Taskが完了したと判断するために必要な、速く局所的な検証。

例:

- unit test
- type check
- lint
- 対象機能の限定的な動作確認
- 変更箇所の差分レビュー

これは原則としてTask内で実行する。

### Cross-task verification

複数Taskをまとめて確認した方が効率的、または全体状態でないと意味がない検証。

例:

- 全E2E
- 全体performance test
- staging検証
- 物理端末テスト
- 長時間負荷試験
- release candidate review

これは独立Taskとして管理してよい。

ただし、横断テストへ回すことを理由に、各Taskの最低限の完了確認まで省略してはならない。局所的に壊れている変更を大量に積み上げると、最後の統合テストが原因調査大会になる。

## 周期・トリガー

横断Taskは、単なる手動作業に限らない。

Project ProfileまたはWorkflowで、実行条件を定義できる。

例:

```yaml
verification_tasks:
  integration-e2e:
    trigger:
      completed_tasks: 5
    actor: agent
    cost: high

  weekly-security-audit:
    trigger:
      schedule: weekly
    actor: agent
    cost: high

  release-review:
    trigger:
      event: release-candidate
    actor: human-and-agent
    cost: very-high
```

AOTP 1.0ではこのtrigger実行基盤自体は未実装であり、将来Hookやschedulerと接続する対象とする。

## TaskとAgentの関係

AgentはTaskに従属する実行主体であり、Agentそのものを作業管理の基本単位にはしない。

```text
Task
 ├─ assigned: claude-code / implementer
 ├─ reviewer: codex / reviewer
 ├─ model: ...
 ├─ budget: ...
 └─ result: ...
```

一つのTaskに複数Agentが関与してもよい。実装、調査、レビューなど役割が独立している場合は、Task内の実行記録として残すか、成果・失敗・コストを独立管理する必要がある場合はTaskを分ける。

## 基本原則

AOTPの作業構造は次を基本とする。

```text
大きな目的          → Epic
実際に進捗を持つ仕事 → Task
Task内の小さな工程    → checkpoint
高コストな横断検証    → 独立Task
実行主体             → Agent / Human
```

中心は常にTaskである。EpicやAgent、Hook、TestはTaskを管理・実行・検証するための補助構造として扱う。
