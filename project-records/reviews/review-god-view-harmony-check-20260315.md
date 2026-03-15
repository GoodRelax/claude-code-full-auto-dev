``````markdown
# 「神の視点」全体調和チェック報告

**レビュー日:** 2026-03-15
**対象:** リポジトリ全体（CLAUDE.md, process-rules, document-rules, essays, agents, prompt/）
**適用観点:** 設計哲学の一貫性、責務分離、プロセスフロー完全性、数値整合性、新旧残骸

---

## サマリー

| 区分 | 件数 |
|------|------|
| 矛盾・不調和（要修正） | 10 |
| 軽微な不整合（推奨修正） | 5 |
| 調和している点 | 8 |

---

## チェック1: 設計哲学の一貫性

### 調和している点

1. **STFB（上剛下柔）の一貫適用** -- ANMS essay の章構成原則が process-rules のフェーズ定義（安定: Foundation/Requirements → 可変: Implementation/Testing）と構造的に対応している。仕様書の章番号と開発フェーズの方向が一致。

2. **Clean Architecture / DIP** -- CLAUDE.md のコーディング規約、Agent Teams 定義（implementer に「Clean Architecture・DIPを遵守」を明記）、document-rules の external-dependency-spec（DIPによる抽象化の根拠を Ch5 差し替え戦略で定義）、条件付きプロセスのHW/AI/FW区分（Framework層の分離）が一貫。

3. **命名は言霊** -- CLAUDE.md のコーディング規約で汎用語禁止を明記。document-rules §7 の名前空間命名規則（略称禁止、file_type名をそのまま使用）が同原則を体現。Form Block のフィールド名も `decision_status`, `defect_status`, `risk_status`, `cr_status` と汎用語 `status` を回避する命名になっている。

4. **抽象→具体の順序** -- process-rules の章構成が Part 1（全体像）→ Part 2（フェーズ詳細）→ Part 3（設定と準備）→ Part 4（品質・運用）の順。document-rules も §1 概要 → §4 ブロック構造 → §7 具体タイプ → §9 各Form Block定義の順。essay も Abstract → Introduction → Proposed Approach → Discussion の順。

5. **SRP（単一責任原則）の設計文書への適用** -- 仕様書を spec-foundation（srs-writer所有）と spec-architecture（architect所有）に分割。変更管理の分離（change-request = ユーザー起点、defect = AI起点のバグ、decision = AI起点の設計判断）が明確。

---

## チェック2: 責務の分離

### 調和している点

6. **11エージェントの責務分離** -- agents/ に11ファイルが存在し、CLAUDE.md の Agent Teams 設定と1:1で対応。各エージェントが document-rules §11 のオーナーシップモデルで明確に管理するfile_typeを持つ。重複する所有権はない。

7. **lead vs implementer の分離** -- lead.md は「オーケストレーション・フェーズ遷移・品質ゲート・decision記録」、implementer.md は「src/ 配下のコード実装・単体テスト作成」と明確に分離。SRPが適用されている。

8. **change-request = ユーザー起点 / defect・decision = AI起点** -- change-manager.md に「change-request はユーザー起点の変更のみを扱う。AI側の技術的変更は defect または decision で管理する」と明記。document-rules §9.8 の cause enum（requirement-addition / requirement-change / scope-change）もユーザー起点のみを列挙しており一貫。

### 矛盾・不調和

**[M-01] process-rules 7.3.1 のchange-manager定義に旧い cause enum が残存**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:1830`
- **問題:** process-rules 内の change-manager エージェント定義（§7.3.1）で「変更要求の起因（バグ/要件追加/仕様変更）」と記述されている。一方、実際の agents/change-manager.md と document-rules §9.8 では「ユーザー起点のみ（requirement-addition / requirement-change / scope-change）」に限定済み。
- **影響:** バグをCRとして扱う旧ルールの残骸。エージェントがprocess-rulesを読んだ場合に混乱する。
- **修正案:** process-rules 7.3.1 の記述を「変更原因（requirement-addition / requirement-change / scope-change）」に修正し、バグはdefectで管理する旨を注記。

**[M-02] process-rules 7.3.1 に change-log.md への追記手順が残存**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:1825`
- **問題:** 「承認された変更は project-records/change-requests/change-log.md に追記する」と記述。しかし change-log.md は document-rules §7 の file_type に未登録。agents/change-manager.md からは既に削除済み（整合性チェックセッション #26 で対応済み）。
- **影響:** process-rules にのみ旧い参照が残っている。エージェントが実行すると file_type 不明のファイルが作成される。
- **修正案:** process-rules 7.3.1 から change-log.md の参照を削除。変更管理の記録は各change-request-{NNN}.md の cr_status で追跡し、Footer の change_log で履歴管理する方針に統一。

**[M-03] process-rules §3.2.1 の出力にも change-log.md が記載**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:338`
- **問題:** 変更管理フローの出力として「project-records/change-requests/change-log.md（変更管理台帳）」が記載。M-02と同根の問題。
- **修正案:** 同箇所から change-log.md を削除。

---

## チェック3: プロセスフローの完全性

### 調和している点（追加）

- Phase 0→1→2→3→4→5→6 のフローが process-rules §2.2 のMermaid図で途切れなく接続されている。
- 各フェーズの品質ゲート（R1→R2/R4/R5→R2-R5→R6→R1-R6）が process-rules §9.1 と document-rules §7.1 ワークフロー参照テーブルの review file_type で整合。
- document-rules §7.1 のフェーズ名が process-rules §2.1 の7フェーズ定義（phase-setup / phase-planning / phase-dependency-selection / phase-design / phase-implementation / phase-testing / phase-delivery）と完全一致。

### 矛盾・不調和

**[M-04] risk-register のフォーマット不一致: JSON vs Markdown**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:354` および `.claude/agents/risk-manager.md:34`
- **問題:** process-rules §3.2.2 と agents/risk-manager.md ではリスク台帳を `risk-register.json` と定義。一方、document-rules §3.3 および §10 では `risk-register.md`（Markdown シングルトン、Common Block付き）と定義。CLAUDE.md でも `risk-register.md` と記載。
- **影響:** risk-manager がJSON形式で出力するとCommon Blockが適用されず、フレームワークの文書管理体系から外れる。
- **修正案:** process-rules §3.2.2 と agents/risk-manager.md を `risk-register.md`（Markdown形式、Common Block + risk: Form Block付き）に統一。JSON例はForm Block + Detail Block内のテーブルに置換。

**[M-05] vulnerability-report.md が file_type に未登録**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:492, 1559` および `.claude/agents/security-reviewer.md:30`
- **問題:** process-rules §3.2.8 と agents/security-reviewer.md で `docs/security/vulnerability-report.md` を出力すると記載。しかし document-rules §7 には file_type `vulnerability-report` は未登録。整合性チェック（project-consistency-check-20260315.md L-03）で指摘済みだが未修正。
- **影響:** security-scan-report（SAST/SCA/DAST/手動を統合）が既に §7 に登録されており、vulnerability-report との関係が曖昧。
- **修正案:** vulnerability-report を security-scan-report に統合。process-rules と agents/security-reviewer.md の参照を `project-records/security/security-scan-report-{NNN}-*.md` に修正。

**[M-06] traceability-matrix のフォーマット不一致: JSON vs Markdown**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:382-396`
- **問題:** process-rules §3.2.3 でトレーサビリティマトリクスを `traceability-matrix.json` と定義。document-rules §7 および §9.9 では `traceability-matrix.md`（Markdown シングルトン、Common Block + traceability: Form Block付き）。
- **影響:** M-04と同種。JSONで出力するとフレームワークの管理体系から外れる。
- **修正案:** process-rules §3.2.3 を Markdown 形式に統一。JSON例はDetail Block内のテーブル形式に置換。

**[M-07] defect の命名規則不一致: DEF-{番号}.md vs defect-{NNN}.md**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:415`
- **問題:** process-rules §3.2.4 で「DEF-{番号}.md（障害票）」と記載。document-rules §3.3 では「defect-012-20260316-140000.md」（file_type-NNN-timestamp形式）。defect:id フィールド（Form Block内）の値が DEF-NNN であり、ファイル名ではない。
- **影響:** ファイル命名がdocument-rules の規則に沿わない。
- **修正案:** process-rules §3.2.4 の出力を `project-records/defects/defect-{NNN}-{YYYYMMDD}-{HHMMSS}.md` に修正。DEF-NNN は Form Block の defect:id フィールドの値である旨を明記。

**[M-08] final-report.md の作成フェーズ不一致: Phase 5 vs Phase 6**

- **箇所:** `process-rules/full-auto-dev-document-rules-ja.md:184`
- **問題:** document-rules §3.5 で「総括レポート | final-report.md | シングルトン。Phase 5で作成」と記載。しかし agents/lead.md:23 は「Phase 6 で final-report.md を作成する」、README.md:81 は「Phase 6」、document-rules §7.1 ワークフロー参照テーブルは commissioned_by = `phase-delivery`（= Phase 6）。process-rules §2.2 のフローチャートでもPhase 6の中にfinal-reportがある。
- **影響:** document-rules の1箇所のみ旧い「Phase 5」が残っている。
- **修正案:** document-rules §3.5 の備考を「Phase 6で作成」に修正。

---

## チェック4: 数値の一貫性

### 整合確認結果

| テーブル | 行数 | 整合状況 |
|---------|------|---------|
| §7 file_type テーブル | 25具体タイプ + 1抽象テンプレート = 26 | OK |
| §8 名前空間テーブル | doc: (1) + 25具体タイプ用 (25) = 26行 | OK -- §7と完全対応 |
| §11 オーナーテーブル | 25行（handoff を除く全具体タイプ） | OK -- handoff は §7.1 で from-agent が owner と定義されており §11 での省略は妥当 |
| §7.1 ワークフロー参照テーブル | 25行（handoff 含む全具体タイプ） | OK -- §7 と完全対応 |
| handoff.md のタイプ数記載 | 「26タイプ（25具体 + 1抽象テンプレート）」 | OK -- §7 と一致 |

### 矛盾・不調和

**[L-01] performance-report の NFR不合格フェーズ記載不一致**

- **箇所:** `process-rules/full-auto-dev-document-rules-ja.md:392`
- **問題:** §4.2 実例6 で「100%でないとPhase 4不合格」と記載。しかし性能テストは process-rules §2.2 で Phase 5（testing）に配置されている。
- **修正案:** 「Phase 5不合格」に修正。

---

## チェック5: 新旧の残骸

### prompt/ 配下のファイル（10ファイル + old/）

| ファイル | 判定 | 理由 |
|---------|------|------|
| `document-rules-handoff.md` | **現用** | 文書管理規則の引継ぎ文書。未着手タスクが残っている |
| `block-placement-examples.md` | **要判断** | handoff.md #3 で「§4.2に統合済み。削除 or 教育資料として残すか」と記載。判断保留中 |
| `prompt.md` | **旧残骸** | 内容は「sast-report / vulnerability-report の意味が分からん。解説せよ」等の一時的メモ。security-scan-report への統合完了後に不要 |
| `project-review.md` | **旧残骸** | 整合性チェックの旧プロンプト。project-consistency-check-20260315.md として成果物化済み |
| `naming-decision-anps.md` | **旧残骸** | ANPS命名検討の一時ファイル。決定事項は document-rules に反映済み |
| `review-handoff.md` | **要確認** | レビュー関連の引継ぎ。内容を確認して完了していれば削除可 |
| `angs-essay-review-handoff.md` | **要確認** | ANGS論文レビューの引継ぎ |
| `manual-finetune-handoff.md` | **要確認** | 手動調整の引継ぎ |
| `semi-auto-sw-dev-prompt.md` | **旧残骸** | 「ほぼ全自動」以前の半自動開発プロンプト。現在のフレームワークに不要 |

### 矛盾・不調和

**[M-09] prompt/ 配下に旧残骸が4件残存**

- **問題:** prompt.md, project-review.md, naming-decision-anps.md, semi-auto-sw-dev-prompt.md は現在のフレームワークで不要。
- **修正案:** prompt/old/ に移動（タイムスタンプ付与）。

**[M-10] process-rules §3.3.4 と §3.3.1 に旧いディレクトリパスが残存**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:504, 549`
- **問題:** §3.3.1 で `project-records/release/release-checklist.md`、§3.3.4 で `project-records/archive/` を参照。しかし handoff #27 で「archive/ + release/ → snapshots/ に統合」と記載されており、document-rules §2 のディレクトリ構成には `project-records/snapshots/` のみが存在。release/ と archive/ は旧体系の残骸。
- **影響:** エージェントが存在しないディレクトリにファイルを出力しようとする。
- **修正案:** release-checklist.md は project-management/ 等の適切な場所に移動、archive/ の参照を old/ ディレクトリルール（document-rules §3.10）に置換。

---

## 追加発見事項（軽微）

**[L-02] document-rules §4.2 実例5 の spec Form Block フィールドが旧称を使用**

- **箇所:** `process-rules/full-auto-dev-document-rules-ja.md:377-381`
- **問題:** 実例5 で `spec:format`, `spec:draft_status`, `spec:completed_chapters`, `spec:fr_count`, `spec:nfr_count` と `spec:` 名前空間を使用。しかし §7 では spec-foundation: と spec-architecture: に分割済み。
- **修正案:** 実例5 を spec-foundation: / spec-architecture: の名前空間で書き直す。

**[L-03] CLAUDE.md テンプレートの spec-template パス**

- **箇所:** `CLAUDE.md:37`
- **問題:** 「テンプレート: process-rules/spec-template-ja.md」と記載。このファイルが存在するか要確認。
- **影響:** 軽微。テンプレートファイルはプロジェクト適用時に配置する前提。

**[L-04] CLAUDE.md Agent Teams の Implementation Agent 名称**

- **箇所:** `CLAUDE.md:111`
- **問題:** 「Implementation Agent（implementer）」と記載。本文中では他の箇所で「Implementer Agent」とも呼ばれる。CLAUDE.md 自体の中では一貫しているが、system prompt（本チャットのレビュー観点）では「Implementation Agent」と「Implementer Agent」が混在。
- **影響:** 軽微。agents/ のファイル名 `implementer.md` が正。

**[L-05] process-rules §3.2.8 の出力先が docs/security/**

- **箇所:** `process-rules/full-auto-dev-process-rules-ja.md:492`
- **問題:** 「docs/security/vulnerability-report.md に記録する」と記載。document-rules ではセキュリティスキャン結果は `project-records/security/`（プロセス記録側）に配置。設計成果物（docs/security/）にスキャン結果を置くのは分離原則に反する。
- **修正案:** M-05 の修正と合わせて `project-records/security/` に統一。

---

## 修正優先度

| ID | 重要度 | 修正箇所 |
|----|--------|---------|
| M-04 | High | process-rules §3.2.2 + agents/risk-manager.md（JSON→MD形式統一） |
| M-05 | High | process-rules §3.2.8 + agents/security-reviewer.md（vulnerability-report→security-scan-report統一） |
| M-06 | High | process-rules §3.2.3（JSON→MD形式統一） |
| M-01 | Medium | process-rules §7.3.1（旧cause enum修正） |
| M-02 | Medium | process-rules §7.3.1（change-log.md削除） |
| M-03 | Medium | process-rules §3.2.1（change-log.md削除） |
| M-07 | Medium | process-rules §3.2.4（defect命名規則修正） |
| M-08 | Medium | document-rules §3.5（Phase 5→Phase 6修正） |
| M-10 | Medium | process-rules §3.3.1/§3.3.4（旧ディレクトリパス修正） |
| M-09 | Low | prompt/ 旧残骸の整理 |
| L-01 | Low | document-rules §4.2 Phase番号修正 |
| L-02 | Low | document-rules §4.2 実例5 名前空間修正 |
| L-05 | Low | process-rules §3.2.8 出力先修正（M-05と同時対応） |

---

## 総合判定

**全体として調和度は高い。** 設計哲学（STFB、Clean Architecture/DIP、命名は言霊、抽象→具体、SRP）は CLAUDE.md / document-rules / process-rules / essay の全文書にわたって一貫して適用されている。11エージェントの責務分離も明確で、document-rules の25ファイルタイプとの対応が破綻なく成立している。

**主な不調和の原因は「整合性チェックセッションの修正漏れ」である。** 2026-03-15 のセッションで agents/ と document-rules は修正されたが、process-rules 本体（特に §3 の必須プロセス定義と §7 のエージェント定義テンプレート）に旧い記述（JSON形式のフォーマット例、change-log.md 参照、vulnerability-report、旧ディレクトリパス）が残っている。これらは document-rules 側の決定と矛盾しており、エージェントが process-rules を読んだ場合に不整合な出力を生成するリスクがある。

**推奨アクション:** High の 3件（M-04, M-05, M-06）を優先的に修正。いずれも process-rules 内の JSON フォーマット例を document-rules の Markdown + Common Block 体系に統一する作業。
``````
