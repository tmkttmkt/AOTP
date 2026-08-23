---
aotp_version: 2
id: mesu-collection-adopter-feedback
title: "mesu_collection導入からのフィードバック"
md_class: history
history_type: proposal
state: proposed
agent: claude-code
created_at: 2026-08-21T21:00:00+09:00
updated_at: 2026-08-21T21:00:00+09:00
relations: []
---

# mesu_collection導入からのフィードバック

## Context

`C:\TK\github\e-collection\game`（牝コレクション / mesu_collection）が、AOTP v2の最初の実地導入プロジェクトとなった。導入にあたり、正本ドキュメント（`aotp.md`, `document-format.md`, `challenges.md`, `aotp-installer.md`）と、既存の実地適用例である`C:\TK\github\rails_on_rail`（同じくAOTPをベースに`docs/aotp.md`へ独自解釈を追記して運用中。`docs/tasks/`に24件以上の実Taskを保持）の両方を参照した。

この過程で、正本の記述と実地適用の間に3点のズレ・改善余地が見つかった。詳細な調査記録は`C:\TK\github\e-collection\game\docs\research\2026-08-21_aotp-adopter-feedback.md`に残している。

## Proposal

### 1. history文書のfrontmatterフィールド名を簡略化する

正本`document-format.md`はhistory文書に`history_type` / `state` / `agent`を必須とするが、実地適用済みのrails_on_railは`type` / `status` / `owners`という簡略名を独自に採用しており、mesu_collectionもrails_on_rail側を踏襲した。**2つの実地適用プロジェクトが独立に正本と異なる簡略形へ収束している。**

`history_type` → `type`、`state` → `status`、`agent` → `owners`（複数担当を許容する配列）への変更、またはこれらを同義語として両対応することを提案する。

### 2. 検証スクリプトの参考実装を追加する

`challenges.md`の課題1（Loop Engineering的な実行基盤）・課題4（文書制約の強化）は、現時点で「今後の課題」のまま実装例がない。mesu_collectionでは、標準ライブラリのみで完結する最小実装として次を作成し、実際に動作させた。

- `domain/`の禁止import・依存方向逆転をastで検知するスクリプト
- `docs/`のfrontmatter必須項目・md_class妥当性・history文書のid一意性・status値・ファイル命名規則を検証するスクリプト（フルYAMLパーサではなく、frontmatterの単純な`key: value`行だけを解析する軽量パーサ）

いずれも`C:\TK\github\e-collection\game\scripts\check_architecture.py`, `check_docs.py`に実在する。AOTP本体の`docs/aotp-installer.md`セクション14が構想する`docs/scripts/check-docs.py`等の最初の実装例として、参考実装をAOTP本体（例: `templates/scripts/`）へ移植することを提案する。

### 3. データ層の正本形式をプロジェクトの実行環境に委ねる

AOTP自体はデータ形式を規定していないが、実地適用例のrails_on_railは（TypeScript/Node環境のため）YAMLを正本としている。mesu_collectionは標準ライブラリのみで動作する制約（PyPIアクセスが不安定な開発環境のため）を`pyproject.toml`側で既に確定させており、YAMLパーサを追加導入せずJSONを正本とした。

`aotp.md`または`aotp-installer.md`に「データ層の正本形式（YAML/JSON等）はプロジェクトの実行環境・依存方針に従って選択してよい」という一文を明記することを提案する。

## Rationale

- フィールド名の簡略化: 2件という少ないサンプルではあるが、両方とも独立に同じ方向（簡略化）へ収束した事実は、正本側の設計が実運用にとって冗長である可能性を示唆する。
- 検証スクリプト: 「文書を書くだけで終わらせない」というAOTPの理念自体を、AOTP本体が体現できていない状態だった。小さくても動く参考実装があることで、新規導入プロジェクトが「思想は分かったが何から実装すればいいか分からない」状態を避けられる。
- データ形式: AOTPの中核原則（MD公理、外部化、検証可能性）はMarkdown文書に関するものであり、非Markdownのゲームデータ形式まで単一技術スタックへ固定する必然性はない。

## Impact

- `document-format.md`のhistory文書共通型（セクション5）の改訂が必要。既存のAOTP本体文書（`work-units.md`等、まだ`md_class: history`文書は存在しない）への影響はない。
- 影響を受けるのは今後AOTPを新規導入するプロジェクトの文書テンプレートのみ。
- 参考実装スクリプトの移植は、AOTP本体に初めてPythonコード（現状は完全にMarkdownのみ）を持ち込むことになる。言語選択（Python固定か、擬似コードに留めるか）は別途検討が必要。

## Decision

未決定。AOTP正本側の人間によるレビュー待ち。
