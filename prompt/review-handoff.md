# ANMS統合 引継プロンプト

## 背景

claude-code-full-auto-dev プロジェクトに ANMS (AI-Native Minimal Spec) v0.33 を統合する作業の引継ぎ。
ANMS は EARS・Gherkin・Mermaid を STFB（Stable Top, Flexible Bottom）構成で組み合わせた仕様書テンプレート。
従来の SRS/SWS 分離モデルを、ANMS 単一文書モデルに移行する。

## 完了済み

- ANMS Spec Template v0.33 レビュー・23項目修正済み
- ANMS Essay を v0.33 テンプレに同期済み
- Ch6 Design Principles テーブルに SDP 行を追加済み（全テンプレ）
- Design Rationale テーブルに SDP 追加根拠を追記済み
- EN翻訳（テンプレ・Essay）完了
- Dev.to 記事（EN/JA）完了・トーン調整済み
- Zenn 記事（JA）完了・トーン調整済み
- ファイル配置方針 確定済み

## タスク1: ファイル移動・整理

### 1-1. 旧マニュアルを old/ に退避

```bash
mv claude-code-full-auto-dev-manual.md old/claude-code-full-auto-dev-manual_pre-anms.md
```

### 1-2. anms-template/ を作成し正本を配置

SDP追加済みのファイルを使うこと。

```bash
mkdir anms-template

# JA正本（auto-dev ルートから移動）
mv ANMS_Spec_Template.md anms-template/anms-spec-template.md
mv ANMS_Essay.md anms-template/anms-essay.md

# EN翻訳（articles側のSDP済みファイルを持ってくる）
cp ../articles/ai-native-spec/anms-spec-template.md anms-template/anms-spec-template-en.md
cp ../articles/ai-native-spec/anms-essay.md anms-template/anms-essay-en.md
```

### 1-3. spec/ ディレクトリを作成

```bash
mkdir spec
touch spec/.gitkeep
```

### 1-4. docs/srs/ と docs/sws/ を廃止

```bash
rm docs/srs/.gitkeep
rm docs/sws/.gitkeep
rmdir docs/srs
rmdir docs/sws
```

### 1-5. 旧テンプレを old/ に退避

```bash
mv spec-template.md old/
```

### 1-6. articles 側の重複ファイルを削除

```bash
cd ../articles/ai-native-spec
rm anms-spec-template.md
rm anms-spec-template-ja.md
rm anms-essay.md
rm anms-essay-ja.md
```

### 1-7. articles/ai-native-spec/README.md を更新

テンプレ・Essay へのリンクを claude-code-full-auto-dev リポジトリに変更する。

## タスク2: マニュアル ANMS 対応リライト

`old/claude-code-full-auto-dev-manual_pre-anms.md` を基に、新マニュアル `claude-code-full-auto-dev-manual.md` をルートに作成する。

### 変更の本質

| Before | After |
|--------|-------|
| Phase 1: srs-writer が user-order.md → IEEE 830 SRS を生成 | Phase 1: srs-writer が user-order.md + anms-template/ → ANMS 形式の単一仕様書を spec/ に生成 |
| Phase 2: architect が SRS → IEEE 1016 SWS を生成 | Phase 2: architect が spec/ の ANMS Ch3-4 を詳細化（同一文書内） |
| 2文書（docs/srs/SRS.md + docs/sws/SWS.md） | 1文書（spec/[project]-spec.md） |
| docs/srs/, docs/sws/ | spec/ |

### 影響箇所（旧マニュアルの grep 結果: SRS/SWS 参照 100箇所超）

主な変更対象:

1. **目次・章構成**: SRS/SWS の言及をすべて ANMS 用語に
2. **第2章 環境構築 > 2.5 推奨プロジェクト構造**: ディレクトリ構成図を更新
   - `anms-template/` 追加
   - `spec/` 追加
   - `docs/srs/`, `docs/sws/` 削除
3. **第3章 CLAUDE.md**: Agent Teams 設定の SRS Agent, Architect Agent の記述更新
4. **第4章 エージェント定義**:
   - `srs-writer.md`: IEEE 830 SRS → ANMS形式仕様書を生成するよう変更。anms-template/anms-spec-template.md を参照する指示を追加
   - `architect.md`: SRS入力 → spec/ の ANMS 仕様書 Ch3-4 を詳細化する役割に変更
   - `review-agent.md`: SRS/SWS別レビュー → ANMS Ch1-2 に R1、Ch3-4 に R2/R4/R5 を適用
   - `test-engineer.md`: SRS/SWS参照 → spec/ 参照に変更
   - `security-reviewer.md`: SRS参照 → spec/ 参照に変更
5. **第5章 カスタムコマンド**: full-auto-dev.md のフロー更新
6. **第6章 プロセス管理**: 変更管理の「SRS承認後」→「ANMS仕様書承認後」
7. **第7章 ワークフロー**:
   - Phase 1: SRS作成 → ANMS仕様書 Ch1-2 作成
   - Phase 2: SWS作成 → ANMS仕様書 Ch3-6 詳細化
   - Mermaid図（フロー図、ガントチャート、シーケンス図）すべて更新
   - コマンド例の更新
8. **レビュー観点テーブル**: 対象文書列の SRS/SWS → ANMS Ch1-2 / Ch3-4
9. **変更管理**: 「SRS承認後」→「仕様書承認後」
10. **トレーサビリティ**: 要件ID→設計ID→テストID の仕組みは維持。ANMS の `(traces: FR-xxx)` と整合させる

### 変更しない箇所

- Phase 3-5 のコード実装・テスト・納品フローの骨格
- エージェントの基本ロール構成（名前は変えてもいい）
- docs/ 配下の srs/sws/ 以外のディレクトリ
- セキュリティ要件、可観測性要件、品質基準の数値
- CLAUDE.md のコーディング規約・セキュリティ要件セクション

### 注意事項

- 旧マニュアルは 40k tokens 超の大文書。セクション単位で読み→書きすること
- ANMS テンプレ本体（anms-template/anms-spec-template.md）を必ず読んでから作業すること
- ANMS Essay（anms-template/anms-essay.md）の設計根拠を理解した上でマニュアルに反映すること
- MCBSMD フォーマット（CLAUDE.md 記載）に従うこと

## タスク3: README.md 更新

- ディレクトリ構成図を新配置に合わせる
- クイックスタートに anms-template/ の説明を追加
- `user-order.md`（カジュアル入力）→ `spec/`（ANMS正式仕様）の流れを説明

## タスク4: CLAUDE.md 更新

- Agent Teams 設定の SRS Agent / Architect Agent 記述を ANMS 対応に
- `docs/srs/`, `docs/sws/` 参照を `spec/` に
- 「SRS承認後の変更」→「仕様書承認後の変更」

## タスク5: エージェント定義ファイル更新

`.claude/agents/` 配下のファイルをマニュアルの変更に合わせて更新:
- srs-writer.md
- architect.md
- review-agent.md
- test-engineer.md
- security-reviewer.md
- change-manager.md

## タスク6: カスタムコマンド更新

`.claude/commands/` 配下:
- full-auto-dev.md（Phase 1-2 のフロー変更）
- check-progress.md（参照パス変更）

## 最終配置（確定）

### claude-code-full-auto-dev

```
claude-code-full-auto-dev/
├── CLAUDE.md
├── README.md
├── LICENSE
├── user-order.md                           ← カジュアル仕様（人間の入力）
├── claude-code-full-auto-dev-manual.md    ← ANMS対応版マニュアル
│
├── anms-template/                         ← ANMSテンプレート（日英）
│   ├── anms-spec-template.md              ← テンプレ正本（JA）
│   ├── anms-spec-template-en.md           ← テンプレ EN
│   ├── anms-essay.md                      ← 論文正本（JA）
│   └── anms-essay-en.md                   ← 論文 EN
│
├── spec/                                  ← AIが生成する正式仕様書
├── .claude/agents/                        ← エージェント定義（ANMS対応に更新）
├── .claude/commands/                      ← カスタムコマンド（ANMS対応に更新）
├── docs/                                  ← 自動生成成果物（srs/, sws/ 廃止済み）
├── prompt/
├── src/
├── tests/
├── infra/
├── sandbox/
└── old/                                   ← 旧版保管
```

### articles/ai-native-spec

```
articles/ai-native-spec/
├── README.md                    ← auto-dev リポジトリへのリンク
├── devto-article.md             ← EN記事
├── devto-article-ja.md          ← JA記事
├── zenn-article.md              ← JA記事
└── material/
```
