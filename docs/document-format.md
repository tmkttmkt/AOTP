---
aotp_version: 2
title: "AOTP文書形式"
md_class: operation
created_at: 2026-08-14T11:39:00+09:00
updated_at: 2026-08-14T12:27:00+09:00
---

# AOTP文書形式

この文書は、AOTPで管理するMarkdown文書のファイル名、タイトル、YAML frontmatter、日時表現、history文書の型と状態を定義する正本である。

## 1. 適用範囲

原則として `docs/` 配下のAOTP管理対象Markdownに適用する。

`README.md` はリポジトリの入口として扱い、このfrontmatter規則の対象外とする。

## 2. ファイル名

ファイル名は機械処理と安定した参照のため、次の規則に従う。

- ASCII英小文字を使用する
- 単語区切りはハイフンを使う
- 拡張子は `.md` とする
- operation / status 文書では、原則として日付やAOTPリリース番号をファイル名に含めない
- history 文書では、識別に必要な場合に限り日付や版を含めてよい
- history 文書のrepository-relative pathは作成後に変更しない

例:

```text
work-units.md
document-format.md
generalization-examples.md
```

## 3. 共通YAML frontmatter

AOTP管理対象Markdownは、ファイル先頭に次の共通項目を持つ。

```yaml
---
aotp_version: 2
title: "AOTPの作業単位"
md_class: operation
created_at: 2026-08-12T13:37:54+09:00
updated_at: 2026-08-14T11:39:00+09:00
---
```

### `aotp_version`

文書が準拠するAOTP内部文書スキーマ版を表す。

リポジトリ本体の `MAJOR.MINOR.PATCH` リリース番号とは別に扱う。

### `title`

文書の正式タイトルを表す。

- YAMLでは二重引用符で囲む
- frontmatter直後の最初のH1と完全一致させる
- タイトルは日本語を基本とし、文書単体で役割が分かる名称にする

例:

```yaml
title: "AOTPの作業単位"
```

```markdown
# AOTPの作業単位
```

### `md_class`

文書の更新方法を決める分類であり、次の固定値だけを使う。

- `operation`: 現在有効な規則、手順、判断基準
- `history`: 提案、設計、作業、判断、実行結果などの履歴
- `status`: 現在の状態、構成、課題一覧

### `created_at`

文書が最初に作成された日時を表す。

- 初回作成後は変更しない
- 既存文書へ後からfrontmatterを導入する場合は、可能な限りGit履歴上の初回作成日時を使用する

### `updated_at`

文書の意味、仕様、状態が最後に変更された日時を表す。

- 意味、仕様、状態が変わる変更では更新する
- 誤字修正、空白、整形など意味を変えない変更では更新しなくてよい
- history文書では `state`、`agent`、`relations` の変更も更新対象とする

## 4. 日時形式

`created_at` と `updated_at` は、タイムゾーンを含むRFC 3339形式で記述する。

```text
2026-08-14T11:39:00+09:00
```

AOTP本体リポジトリでは原則として `+09:00` を使用する。外部プロジェクトでは、そのプロジェクトで定めたタイムゾーンを使用してよいが、オフセットの省略は禁止する。

## 5. history文書の共通型

`md_class: history` の文書は、共通項目に加えて `id`、`history_type`、`state`、`agent`、`relations` を必須とする。

```yaml
---
aotp_version: 2
id: jwt-auth-implementation
title: "JWT認証を実装する"
md_class: history
history_type: task
state: implementing
agent: codex
created_at: 2026-08-14T12:20:00+09:00
updated_at: 2026-08-14T12:27:00+09:00
relations:
  - type: part_of
    target: history/epics/auth-redesign.md
  - type: implements
    target: history/designs/auth-architecture.md
---
```

### `id`

文書自身の短い論理名を表す。

- ASCII英小文字とハイフンを使ったkebab-caseとする
- 文書タイトルの意味を簡潔な英字名で表す
- repository内で一意とする
- 作成後は原則変更しない
- relationの参照先には使わない

`id` は文書自身を識別・検索しやすくするための名前であり、ファイルの所在を表すものではない。

### `history_type`

history文書の型を表す。現時点で次の固定値を定義する。

- `task`: 実行・記録・検証の基本作業単位
- `design`: 実装や構成の設計を定義する文書
- `epic`: 複数Taskを束ねる上位目的・成果単位
- `proposal`: 採否判断を必要とする提案文書

型ごとに許可される `state` と必須見出しが異なる。

### `state`

文書が表す対象の現在工程を表す。文書そのものの存在状態ではなく、Task、Design、Epic、Proposalの進行状態である。

#### Task

許可値:

```text
draft
ready
implementing
review_ready
reviewing
test_ready
testing
completed
cancelled
```

標準遷移:

```text
draft
  ↓
ready
  ↓
implementing
  ↓
review_ready
  ↓
reviewing
  ↓
test_ready
  ↓
testing
  ↓
completed
```

工程が存在しない場合は中間stateを省略してよい。`cancelled` は作業中止を表す。

#### Design

許可値:

```text
draft
review_ready
reviewing
approved
implemented
rejected
superseded
```

標準遷移:

```text
draft
  ↓
review_ready
  ↓
reviewing
  ├─ approved → implemented
  └─ rejected
```

`superseded` は後続Designに置き換えられた状態を表す。

#### Epic

許可値:

```text
draft
ready
active
completed
cancelled
```

標準遷移:

```text
draft → ready → active → completed
```

Epicでは子Taskが詳細工程を保持するため、レビューやテストのstateは持たせない。

#### Proposal

許可値:

```text
draft
proposed
reviewing
accepted
rejected
withdrawn
superseded
```

標準遷移:

```text
draft
  ↓
proposed
  ↓
reviewing
  ├─ accepted
  └─ rejected
```

`withdrawn` は提案者による取り下げ、`superseded` は後続Proposalによる置換を表す。

### `agent`

そのhistory文書の現在の主担当を表す。

```yaml
agent: codex
```

- Agent名または `human` を短い固定名で記述する
- primaryな担当だけをfrontmatterに持つ
- レビュー担当や補助Agentなど複数主体の詳細は本文またはTaskの実行記録で表現する
- 主担当が変わった場合は `agent` と `updated_at` を更新する

### `relations`

AOTP文書間の構造的関係を機械可読に表現する。

```yaml
relations:
  - type: part_of
    target: history/epics/auth-redesign.md
```

`target` はrepository-relative pathを使用する。`id` はrelation targetとして使用しない。

構造上意味を持つ内部参照は本文のMarkdownリンクだけに依存せず、必ず `relations` に記録する。逆参照は文書へ二重記録せず、機械がrepository全体のrelationを走査して生成する。

現時点でrelation typeは次を使用する。

- `part_of`: 上位Epicなどに属する
- `depends_on`: 対象の完了・成立に依存する
- `based_on`: 調査・根拠・前提に基づく
- `follows`: 決定や方針に従う
- `implements`: 設計・仕様を実装する
- `verifies`: 対象を検証する
- `supersedes`: 旧history文書を置き換える
- `related_to`: 上記に分類できない構造的関連

構造的意味を持たない補足、外部URL、説明用リンクは通常のMarkdownリンクとしてよい。

relationが存在しないhistory文書でも項目自体は省略せず、次のように空配列を記述する。

```yaml
relations: []
```

## 6. history_typeごとの本文型

history文書は、型ごとに次の見出しを最低限持つ。`draft` の段階では内容が空でもよいが、見出し自体は保持する。

### Task

```markdown
## Purpose

## Completion Criteria

## Context

## Execution

## Verification

## Result
```

### Design

```markdown
## Goal

## Requirements

## Design

## Alternatives

## Trade-offs

## Validation
```

### Epic

```markdown
## Goal

## Constraints

## Completion Criteria

## Tasks

## Result
```

### Proposal

```markdown
## Context

## Proposal

## Rationale

## Impact

## Decision
```

## 7. history文書の確定と更新

history文書は作業中のstateでは更新してよい。内容が確定した後は本文を履歴として保持し、原則として意味を書き換えない。

確定後でも、ライフサイクルの事実を反映するために `state`、`agent`、`updated_at`、`relations` を更新してよい。例えば、承認済みDesignが実装された場合の `approved → implemented`、後続文書に置換された場合の `superseded` がこれに該当する。

誤った内容を新しい結論へ書き換えるのではなく、必要に応じて後続history文書を作成し、`supersedes` relationで旧文書へ接続する。

history文書のpathはrelationのアドレスとして使われるため、作成後に移動・改名しない。

## 8. operation / status文書の標準形

operation / status文書は原則として次の形から開始する。

```markdown
---
aotp_version: 2
title: "文書タイトル"
md_class: operation
created_at: 2026-08-14T11:39:00+09:00
updated_at: 2026-08-14T11:39:00+09:00
---

# 文書タイトル
```

## 9. 検証

将来的な文書lintでは、少なくとも次を機械検証対象とする。

### 全文書

- 共通必須項目が存在すること
- `md_class` が固定値のいずれかであること
- `title` と最初のH1が完全一致すること
- 日時がタイムゾーン付きRFC 3339形式であること
- `created_at` が後続変更で書き換えられていないこと
- ファイル名がlower-kebab-caseであること

### history文書

- `id` がrepository内で一意でlower-kebab-caseであること
- `history_type` が定義済み固定値であること
- `state` がその `history_type` で許可された値であること
- `agent` が存在すること
- `relations` が配列として存在すること
- relation typeが定義済み固定値であること
- relation targetが存在するrepository-relative pathであること
- 自己依存や不正な依存循環がないこと
- history pathが作成後に変更されていないこと
- 型ごとの必須見出しが存在すること

既存文書は、この規則を正本として順次移行する。
