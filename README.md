# AOTP

**Version 1.0.0**

AOTP（Agent-Oriented Text Programming）は、個人用のAI運用システムを設計・バージョン管理するためのリポジトリである。

目的は、Codex、Claude Code、Gemini CLIなど複数のAI実行環境を、役割・モデル・権限・コスト・検証方法に応じて使い分け、人間とAIが同じMarkdown基盤から作業できる状態を作ることである。

このリポジトリをAOTPの正本とし、個別プロジェクトはAOTPを利用する側として扱う。

## Core documents

- [AOTP概念・運用原則](docs/aotp.md)
- [AOTP導入・移行仕様](docs/aotp-installer.md)
- [AOTPの汎化例](docs/generalization-examples.md)
- [AOTP 1.0の課題](docs/challenges.md)

## Scope

- 人間とAIの役割・権限・責任の外部化
- Markdownを共通状態として使うエージェント運用
- Codex / Claude Code / Gemini CLI等の使い分け
- モデル・reasoning・コスト・リスクに応じたルーティング
- Skill、Agent、Workflow、Project Profileの管理
- 複数エージェント間の引き継ぎ・並列化・独立レビュー
- 個別プロジェクトへAOTPを導入・更新する仕組み

## Generalization

AOTPはソフトウェア開発だけを対象としない。調査・研究、設計書や資料作成、大規模リファクタリング、旅行計画や個人調査などにも、Task、Research、Decision、Status、Agent、Skillという共通構造を適用できる。

具体例は [AOTPの汎化例](docs/generalization-examples.md) にまとめる。

## Current challenges

AOTP 1.0は思想、文書モデル、導入契約を正本化した段階であり、実行基盤には未実装部分がある。

特に今後は、Hook等を利用したLoop Engineering的な実行、Task単位のAgent・runtime・モデル・コスト管理、資料・コンテキスト制約、文書schema、Runtime Adapter、自動導入と監査性を強化する。

詳細は [AOTP 1.0の課題](docs/challenges.md) を正本とする。

## Versioning

AOTP本体のリリース版は `VERSION` で管理する。今回の独立リポジトリ化を最初の正式版 **1.0.0** とする。

文書内の `aotp_version` は、導入対象プロジェクトの文書・Profile互換性を表す内部スキーマ版であり、リポジトリのリリース版とは別に扱う。

## Repository policy

AOTP固有の思想・共通設定・運用ロジックはこのリポジトリで管理する。

DenUniなど個別プロジェクト固有の仕様、ビルド方法、テスト方法、Project Profileは各プロジェクト側に残す。

AOTPの変更はGit履歴で追跡し、運用文書を現在状態の正本として更新する。
