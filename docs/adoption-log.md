---
md_class: status
---

# AOTP採用実績

AOTPは思想と文書形式の正本化が中心であり、検証機構の多くは未実装である（[課題](challenges.md)参照）。一方で、AOTPという名前が付く前の萌芽期から数えると、6件のプロジェクトで実際に運用されてきた実績がある。「未実用の概念」ではなく、複数年・複数プロジェクトにまたがって鍛えられてきた運用であることを示すため、実際の採用状況をここに記録する。

新しい採用プロジェクトが増えた場合、または既存プロジェクトの状況が大きく変わった場合に、この文書を更新する。個別の経緯は各プロジェクト側のhistory文書を正本とし、ここでは要約のみを持つ（一文書一責務）。

## 系譜（バージョン別）

作者本人からの聞き取りに基づく。各プロジェクトが実際に使っていた「バージョン」は、AOTPという名称・正本化の有無で区分する。

| バージョン | 件数 | 意味 |
|---|---:|---|
| v0 | 1 | AOTPという名前も正本もまだない、萌芽期 |
| v1 | 3 | 「AOTP」と名付けられ運用されたが、正本リポジトリはまだ存在しない時期 |
| v2 | 2 | このリポジトリ（`C:\TK\github\AOTP`）による正本化後 |

v1期の文書（例: `C:\TK\github\DenUni\docs\dev\aotp.md`）に`aotp_version`のfrontmatterが存在しないことは、この区分と実際に整合する（v2の`document-format.md`で初めて`aotp_version`スキーマが導入された）。

## 1. eroge（前作） — v0 — 終了

- 位置づけ: AOTPという名前がつく前の萌芽期。この開発の失敗（`vn_engine.py`単一ファイルへの機能集中）が、「Markdownを使ったテキストプログラミング」という発想の出発点になった。
- 本人の言葉: `vn_engine.py`のファイル肥大化それ自体は副産物であり、根本原因は複数AIエージェント運用の本格化にあった。
- リポジトリ実測（`C:\TK\github\eroge`、2026-08-21確認）による裏付け:
  - 全コミット数は**4件**のみ。最初のコミット（2026-05-05）時点で、`vn_engine.py`は既に3,310行の状態で一括投入されている（53ファイル・14,504行の初回コミット）。最終形は4,298行。
  - つまり実際の開発（無数のAIエージェントセッション・試行錯誤）の大半は、コミット履歴に一切残っていない。粒度の粗いコミットしか無いため、後から「何が起きたか」を復元できない。
  - `proposals/`（4件の設計提案md）・`explanations/`（7件の仕様md）自体は存在しており、文書化の意志はあった。個々の文書（例: `CHARACTER_VALUE_PROPOSAL.md`）は目的・方針・計算式が整理された、単体としては読みやすい文章になっている。
  - **しかし「履歴」として機能していなかった。** `README.md`が事実上の入口になっているが、`explanations/`7件中4件しかリンクしておらず（`ACTION_ROLE_EFFECTS.md`, `CHARACTER_MARKET.md`, `worldview.md`が索引から漏れている）、`proposals/`4件は README からまったく参照されていない。さらに個々の文書に`status`・日付・提案と実装の対応関係を示すfrontmatterが一切なく、ある提案が「採用済みか、却下されたか、まだ検討中か」を文書だけから判断できない。読むには4,298行の`vn_engine.py`と突き合わせるしかなかった。
- 結論: 「ドキュメントを書くこと」自体は足りていたが、複数AIエージェントを本格投入する際に必要な、（1）作業の細分化、（2）索引の鮮度、（3）文書の状態（現在有効か・確定済みの履歴か）の区別、（4）検証可能性、が欠けていた。これはAOTPのMD公理（`md_class`によるoperation/history/status区別）と外部化原則（連鎖リンク、一文書一責務）が直接対応する欠落であり、eroge以降のAOTP各版で繰り返し補強されてきた論点でもある。

## 2. DenUni — v1 — 終了

- パス: `C:\TK\github\DenUni`
- 位置づけ: 「AOTP」という名称が生まれ、大規模に実証された段階。`docs/dev/aotp.md`はAOTP本体の`aotp.md`と同一の本文を持つ（正本`aotp.md`の「DenUniでの位置づけ」節が直接言及している対象）。
- 文書構成: `docs/proposals/`（12件）, `docs/designs/`（11件）, `docs/research/`（5件）, `docs/impl-note/`（5件）, `docs/features/`, `docs/dev/`。旧形式のstatus埋め込みファイル名（例: `2026-06-22_done_refactoring.md`）を使用。
- リポジトリ実測（2026-08-21確認）: 全コミット数**225件**（2026-04-20〜2026-06-30）。erogeが4コミットだったのと対照的に、粒度の細かい履歴が残っている。
- ドキュメント側も実測（例: `docs/proposals/2026-06-23_active_library-account.md`）: v2の`aotp_version` frontmatterはまだ無いが、本文冒頭に**作成日・ステータス（active等）・担当**が明記されている。この非YAML形式の軽量メタデータだけで、「この提案が今どの状態か」を文書単体から判断できる。erogeの`proposals/`（frontmatterも状態表記も一切なし）との対比が明確。
- 本人の評価: 未確認のまま続行したTaskや、途中で廃棄されたTaskなど運用上の課題は残るが、**履歴そのものはしっかり残っている**（＝コミットとドキュメントの両方から、後から何が起きたかを復元できる）。この「復元可能性」こそがerogeとの決定的な違いであり、AOTPが「検証可能性」「MD公理」原則として一般化した性質そのものである。

## 3. アルバイト先iOSプロジェクト（社外秘） — v1 — 継続中

- 位置づけ: 上司を通じて概念のアップデートが行われた段階。Hookとサブエージェントを使うLoop Engineering構想（AOTP正本`challenges.md`課題1に相当）の起点。
- 注記: 社外秘のため、リポジトリの内容は参照・記載しない。本項目は本人からの口頭要約のみに基づく。

## 4. AMAHARA（集団制作Unityゲーム） — v1 — 継続中

- 位置づけ: 概念を多様化・汎用化する一手になったプロジェクト。同時に、汎用化が進んだことで「一貫した正本が欲しい」という動機が生まれ、これが後のAOTP正本リポジトリ（このリポジトリ）の直接のきっかけになった。
- 詳細: 未記録。本人により後日詳細化される予定。

## 5. rails_on_rail — v2 — 継続中

- パス: `C:\TK\github\rails_on_rail`
- 位置づけ: 正本化後、最初の実証プロジェクト。`docs/aotp.md`へ「Rails on Railでの解釈」を追記して運用する。
- 特記事項:
  - 「Epicは開発Phase」という解釈（`phase-minus-one.md`等）。
  - history文書のfrontmatterに`type` / `status` / `owners`という簡略フィールド名を採用（正本`document-format.md`の`history_type` / `state` / `agent`とは異なる）。
  - `data/`ディレクトリにYAMLを正本としたゲームデータを分離する、データと処理の分離構想を確立。

## 6. e-collection/game（mesu_collection、今作） — v2 — 開始直後

- パス: `C:\TK\github\e-collection\game`
- 位置づけ: rails_on_railの解釈をさらに引き継いだ2件目のv2実証プロジェクト。前作`eroge`（v0の起点そのもの）の技術的負債を反省材料として、AOTPを本格導入した。
- 特記事項:
  - `scripts/check_architecture.py`（`domain/`の依存方向をastで検証）と`scripts/check_docs.py`（frontmatter・id一意性・status値・ファイル命名規則を検証）を実装し、実際にゼロ違反で稼働している。AOTP正本自身がまだ持たない検証機構の最初の実装例。
  - rails_on_railのYAML方針をJSONへ置き換えて踏襲（Python標準ライブラリのみで動作する制約のため）。
  - AOTP正本への具体的な改善提案を[`docs/proposals/mesu-collection-adopter-feedback.md`](proposals/mesu-collection-adopter-feedback.md)として提出済み（`state: proposed`、未決定）。

## 観察されるパターン

- **履歴の粒度そのものが最初の教訓**: v0のeroge（4コミット、初回コミットで既に3,310行のvn_engine.pyが一括投入）とv1のDenUni（225コミット、細かい履歴が残る）の対比が、AOTPの「外部化」「検証可能性」原則の実証的な出発点になっている。ドキュメント（`proposals/`, `explanations/`）の存在自体はerogeにもあったが、それだけでは不十分だった。
- **history文書のフィールド名**: v1のDenUniは旧形式（status埋め込みファイル名）。v2のrails_on_railとmesu_collectionはどちらも独立に`type` / `status` / `owners`という簡略名へ収束しており、正本の`history_type` / `state` / `agent`をそのまま採用した例はまだない。
- **データ/処理分離**: rails_on_rail（YAML）が発端となり、mesu_collection（JSON）がそれぞれの実行環境に応じた形式で踏襲している。AOTP本体はデータ形式を規定していない。
- **正本化の動機そのものが実運用から生まれている**: AMAHARAでの汎用化の反動として正本（このリポジトリ）が作られ、その正本を最初に実証したrails_on_railとmesu_collectionが、今度は正本側へのフィードバックを生んでいる。この循環自体がAOTPの「省資源化」「検証可能性」原則の実例になっている。

## 関連

- [AOTP概念・運用原則](aotp.md)
- [AOTP 1.0の課題](challenges.md)
- [mesu_collection導入からのフィードバック](proposals/mesu-collection-adopter-feedback.md)
