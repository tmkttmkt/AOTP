---
aotp_version: 2
title: "AOTP文書形式"
md_class: operation
created_at: 2026-08-14T11:39:00+09:00
updated_at: 2026-08-14T11:39:00+09:00
---

# AOTP文書形式

この文書は、AOTPで管理するMarkdown文書のファイル名、タイトル、YAML frontmatter、日時表現の共通形式を定義する正本である。

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
- ファイルのrepository-relative pathを文書の識別子として扱い、独立した `id` は必須としない

例:

```text
work-units.md
document-format.md
generalization-examples.md
```

## 3. 必須YAML frontmatter

AOTP管理対象Markdownは、ファイル先頭に次の5項目をこの順序で持つ。

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
- `history`: 提案、調査、判断、実行結果などの履歴
- `status`: 現在の状態、構成、課題一覧

### `created_at`

文書が最初に作成された日時を表す。

- 初回作成後は変更しない
- 既存文書へ後からfrontmatterを導入する場合は、可能な限りGit履歴上の初回作成日時を使用する

### `updated_at`

文書の意味、仕様、状態が最後に変更された日時を表す。

- 意味、仕様、状態が変わる変更では更新する
- 誤字修正、空白、整形など意味を変えない変更では更新しなくてよい

## 4. 日時形式

`created_at` と `updated_at` は、タイムゾーンを含むRFC 3339形式で記述する。

```text
2026-08-14T11:39:00+09:00
```

AOTP本体リポジトリでは原則として `+09:00` を使用する。外部プロジェクトでは、そのプロジェクトで定めたタイムゾーンを使用してよいが、オフセットの省略は禁止する。

## 5. 文書先頭の標準形

新規文書は原則として次の形から開始する。

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

## 6. 検証

将来的な文書lintでは、少なくとも次を機械検証対象とする。

- 必須5項目が存在すること
- frontmatterの項目順が標準形と一致すること
- `md_class` が固定値のいずれかであること
- `title` と最初のH1が完全一致すること
- 日時がタイムゾーン付きRFC 3339形式であること
- `created_at` が後続変更で書き換えられていないこと
- ファイル名がlower-kebab-caseであること

既存文書は、この規則を正本として順次移行する。
