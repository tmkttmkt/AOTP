# AOTP

**Version 1.0.0**

AOTP（Agent-Oriented Text Programming）は、個人用のAI運用システムを設計・バージョン管理するためのリポジトリである。

目的は、Codex、Claude Code、Gemini CLIなど複数のAI実行環境を、役割・モデル・権限・コスト・検証方法に応じて使い分け、人間とAIが同じMarkdown基盤から作業できる状態を作ることである。

このリポジトリをAOTPの正本とし、個別プロジェクトはAOTPを利用する側として扱う。

## Core documents

- [AOTP概念・運用原則](docs/aotp.md)
- [AOTP導入・移行仕様](docs/aotp-installer.md)

## Scope

- 人間とAIの役割・権限・責任の外部化
- Markdownを共通状態として使うエージェント運用
- Codex / Claude Code / Gemini CLI等の使い分け
- モデル・reasoning・コスト・リスクに応じたルーティング
- Skill、Agent、Workflow、Project Profileの管理
- 複数エージェント間の引き継ぎ・並列化・独立レビュー
- 個別プロジェクトへAOTPを導入・更新する仕組み

## Versioning

AOTP本体のリリース版は `VERSION` で管理する。今回の独立リポジトリ化を最初の正式版 **1.0.0** とする。

文書内の `aotp_version` は、導入対象プロジェクトの文書・Profile互換性を表す内部スキーマ版であり、リポジトリのリリース版とは別に扱う。

## Repository policy

AOTP固有の思想・共通設定・運用ロジックはこのリポジトリで管理する。

DenUniなど個別プロジェクト固有の仕様、ビルド方法、テスト方法、Project Profileは各プロジェクト側に残す。

AOTPの変更はGit履歴で追跡し、運用文書を現在状態の正本として更新する。
