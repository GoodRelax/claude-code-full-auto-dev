# 引継ぎ: 文書管理規則・プロセス規則改修

## 前回までの成果

### 完了済み

1. **ブロック命名変更** — Type Block → Form Block、Body → Detail Block
2. **Common Block拡張** — 14フィールド（4追加 + AIの読取フロー順に並べ替え）
3. **名前空間命名規則** — file_type = 名前空間。略称禁止
4. **spec分割** — spec-foundation + spec-architecture
5. **user-order.md リネーム** — 全ファイル51箇所置換完了
6. **バージョニング** — v0.0.0（PoC前）。MAJOR/MINOR/PATCH定義
7. **メタテンプレート** — Fields + Detail Block Guidance の2セクション
8. **7フェーズ体系** — Phase 2: dependency-selection を新設
9. **外部依存モデル** — external-dependency-spec テンプレート + 3子タイプ（HW/AI/FW）
10. **条件付きプロセス** — 5→8項目に拡張（HW連携、AI/LLM連携、フレームワーク要件定義を追加）
11. **プロセス規則の章構成再編** — 抽象→具体の順序に（Part 1: 全体像、Part 2: フェーズ詳細、Part 3: 設定）
12. **フローチャート** — 縦方向（flowchart TD）に変更、7フェーズ対応
13. **anms-template/ 解体** — essays/ + process-rules/spec-template に分離
14. **多言語対応** — doc:language追加、主言語=サフィックスなし方式
15. **全Phase (0-6) でHW/AI/FW条件付きタスク記載** — Phase 1ヒアリング〜Phase 6納品まで
16. **CRの戻りルート** — dependency-selectionフェーズへの戻り、cause: dependency-change追加
17. **CLAUDE.md** — 重要判断に外部依存選定追加、条件付きプロセス8項目化

### ファイルタイプ数: 26タイプ（§7登録済み。security-scan-report追加で25具体 + 1抽象テンプレート）

### 整合性チェックセッション（2026-03-15 後半）で完了した追加作業

18. **プロジェクト全体整合性チェック** — 8観点で23件の不整合を検出・修正
19. **旧6フェーズモデルの残骸を全面修正** — commands/full-auto-dev.md, agents/review-agent.md, agents/license-checker.md, README.md, document-rules §9/§7.1
20. **旧パス修正** — commands/check-progress.md, commands/retrospective.md
21. **旧用語「Type Block」「Body」の全置換** — document-inventory.md, block-placement-examples.md
22. **agents/lead.md 作成** — orchestrator の責務を明確化
23. **agents/implementer.md 作成** — 実装担当を SRP で分離
24. **security-scan-report を§7/§8/§11に登録** — sast-report を統合。scan_type: sast/sca/dast/manual で区別
25. **change-request を「ユーザー起点のみ」に限定** — cause enum を requirement-addition/requirement-change/scope-change に修正。AI側変更は defect/decision で管理
26. **agents/change-manager.md 修正** — change-log参照削除、ユーザー起点の明記
27. **ディレクトリ整理** — archive/ + release/ → snapshots/ に統合。improvement/, legal/, safety/ を§2に追記
28. **CLAUDE.md Agent Teams 更新** — lead/implementer追加、エージェント名(agent-file-name)を併記
29. **§3.45 → §3.5 セクション番号正規化** — 以降§3.6〜§3.10に繰り下げ
30. **essays/anms-essay-ja.md** — References番号重複修正

### Round 3: process-rules同期 + 要求工学強化

31. **process-rules §3.2.1** — change-log.md参照を削除
32. **process-rules §3.2.2** — risk-register: JSON→MD形式に統一
33. **process-rules §3.2.3** — traceability-matrix: JSON→MD形式に統一
34. **process-rules §3.2.4** — defect命名: DEF-{番号}.md → defect-{NNN}-{timestamp}.md
35. **process-rules §3.2.8 + §7.2.3 + agents/security-reviewer.md** — vulnerability-report → security-scan-report統一、出力先をproject-records/security/に修正
36. **process-rules §7.3.1** — change-manager: 旧cause enum修正、change-log.md削除、ユーザー起点のみ明記
37. **process-rules §3.3.1** — release/release-checklist → project-management/release-checklist
38. **process-rules §3.3.4** — archive/ → old/（document-rules §3.10参照）
39. **document-rules §3.5** — final-report: Phase 5→Phase 6
40. **document-rules §4.2** — spec:名前空間 → spec-foundation:/spec-architecture:、Phase 4→Phase 5
41. **agents/risk-manager.md** — risk-register.json → risk-register.md
42. **prompt/ 旧残骸** — prompt.md, project-review.md, naming-decision-anps.md, semi-auto-sw-dev-prompt.md を prompt/old/ に移動
43. **Phase 1 ヒアリング「ドメイン境界識別」追加** — process-rules §4.2.2 + full-auto-dev.md。「このプロジェクト固有のコアロジックは何か？」「この概念はDomainかFrameworkか？」
44. **Phase 3 設計「レイヤー仕訳」サブフェーズ追加** — process-rules §4.4 + full-auto-dev.md。Ch3作成前にEntity/UC/Adapter/Frameworkの4層分類を明示的に実施
45. **R2 レイヤー仕訳レビュー観点追加** — review-agent.md CA項目 + process-rules §9.2 R2。仕訳の正しさ・Adapter層の適切さをチェック
46. **R1 要求工学的レビュー観点追加** — review-agent.md R1a/R1b分割 + process-rules §9.2 R1。曖昧性・否定形・受動態・異常系網羅・仕様DRY・競合検出

## 未着手タスク（優先度順）

### 高優先度

| # | タスク | 説明 | 対象ファイル |
|---|-------|------|-------------|
| 1 | **Phase 1にモック/サンプル工程追加** | ヒアリング時にUIモック/サンプルを提示してフィードバックを得るイテレーション。言葉だけではUIは伝わらない | process-rules |
| 2 | **新規16タイプのForm Block定義** | §9.10〜§9.25。メタテンプレート（Fields + Detail Block Guidance）に従って定義。security-scan-report が追加され計16タイプ | document-rules §9 |
| 3 | **block-placement-examples.md統合検討** | prompt/block-placement-examples.md の6実例は§4.2に既に統合済み。元ファイルを削除するか、教育資料として残すか判断 | prompt/ |

### 中優先度

| # | タスク | 説明 | 対象ファイル |
|---|-------|------|-------------|
| 4 | **AI/プロンプトのスコープ整理** | SWの対象にAI/プロンプトを含む。AIはフレームワーク層（HWと同列、DIPで抽象化） | process-rules, spec-template |
| 5 | **英語版の規則文書** | process-rules-en.md, document-rules-en.md | process-rules/ |

## 新規15タイプのForm Block定義対象

§9に既に定義済みの9タイプ: pipeline-state, handoff, review, decision, risk, progress, defect, change-request, traceability

以下が未定義（§9.10〜§9.24）:

| # | file_type | 名前空間 | owner |
|---|-----------|---------|-------|
| 10 | hearing-record | hearing-record: | srs-writer |
| 11 | wbs | wbs: | progress-monitor |
| 12 | test-plan | test-plan: | test-engineer |
| 13 | spec-foundation | spec-foundation: | srs-writer |
| 14 | spec-architecture | spec-architecture: | architect |
| 15 | threat-model | threat-model: | security-reviewer |
| 16 | security-architecture | security-architecture: | security-reviewer |
| 17 | observability-design | observability-design: | architect |
| 18 | hw-requirement-spec | hw-requirement-spec: | architect |
| 19 | ai-requirement-spec | ai-requirement-spec: | architect |
| 20 | framework-requirement-spec | framework-requirement-spec: | architect |
| 21 | executive-dashboard | executive-dashboard: | lead |
| 22 | final-report | final-report: | lead |
| 23 | user-order | user-order: | srs-writer |
| 24 | license-report | license-report: | license-checker |
| 25 | performance-report | performance-report: | test-engineer |
| 26 | security-scan-report | security-scan-report: | security-reviewer |

## 設計原則（次のセッションでも遵守すべき）

- **命名は言霊**: type, data, info, value 等の汎用語禁止
- **抽象→具体の順序**: 設計もドキュメントも常に抽象から具体へ
- **SRP**: 1フェーズ1責務、1ファイル1責務
- **DIP**: 外部依存（HW/AI/FW）はAdapter層で抽象化。差し替え可能にする
- **Common Blockとの重複チェック**: Form Block定義時に「全タイプ共通か？」のテストを適用
- **AIの読取フロー最適化**: Common Blockの順序は「識別→状態→ワークフロー→コンテキスト→参照→出自」

## 7フェーズ構成

| Phase | Name | 責務 | 条件付き？ |
|:-----:|------|------|:---------:|
| 0 | setup | 条件評価・CLAUDE.md | No |
| 1 | planning | ヒアリング＆仕様 | No |
| 2 | dependency-selection | 外部依存の評価・選定・調達 | **Yes** |
| 3 | design | 設計 | No |
| 4 | implementation | 実装 | No |
| 5 | testing | テスト | No |
| 6 | delivery | 納品 | No |

## 外部依存モデル

```
external-dependency-spec（抽象テンプレート — 文書管理規則§7に記載）
  ├── hw-requirement-spec（物理デバイス）
  ├── ai-requirement-spec（AI/LLM）
  └── framework-requirement-spec（非標準I/Fフレームワーク）

共通6章構成: 目的→要件→I/F定義→制約→差し替え戦略→その他
```

判断基準: I/F標準度が低い外部依存 → 専用requirement-spec作成。高い場合 → decision記録で十分。

## 条件付きプロセス（8項目）

1. 機能安全（HARA/FMEA）
2. 法規調査
3. 特許調査
4. 技術動向調査
5. アクセシビリティ（WCAG 2.1）
6. HW連携
7. AI/LLM連携
8. フレームワーク要件定義
