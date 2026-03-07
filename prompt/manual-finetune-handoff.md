# 引継ぎプロンプト: マニュアル微調整セッション

## 前提

`prompt/review-handoff.md` で定義された ANMS v0.33 統合タスク（全6件）はすべて完了済み。

| タスク | 内容 | 状態 |
|--------|------|------|
| Task 1 | ANMS Spec Template 作成 | 完了（前セッション） |
| Task 2 | マニュアル書き直し（40k+ tokens） | 完了（本セッション） |
| Task 3 | full-auto-dev.md コマンド更新 | 完了（前セッション） |
| Task 4 | srs-writer.md エージェント更新 | 完了（前セッション） |
| Task 5 | architect.md エージェント更新 | 完了（前セッション） |
| Task 6 | review-agent.md エージェント更新 | 完了（前セッション） |

## 変更の本質（SRS/SWS → ANMS）

- **旧モデル**: IEEE 830 SRS + IEEE 1016 SWS の2文書体制
  - SRS は `docs/srs/` に配置、SWS は `docs/sws/` に配置
  - SRS Agent が SRS を作成、Architect Agent が SWS を作成
- **新モデル**: ANMS (AI-Native Minimal Spec) 単一文書体制
  - `spec/[project-name]-spec.md` に統合
  - Ch1-2 (Foundation/Requirements) = 旧SRS相当 → srs-writer が作成
  - Ch3-6 (Architecture/Specification/Test Strategy/Design Principles) = 旧SWS相当 → architect が詳細化
  - STFB構造（Stable Top, Flexible Bottom）で安定度による層分離

## 今回の作業対象

`claude-code-full-auto-dev-manual.md` の細部修正。ユーザーが気になった箇所を指示するので、それに従って修正する。

## 主要ファイル

| ファイル | 説明 |
|----------|------|
| `claude-code-full-auto-dev-manual.md` | 修正対象のマニュアル本体（~2676行） |
| `old/claude-code-full-auto-dev-manual_pre-anms.md` | ANMS統合前の旧マニュアル（参照用） |
| `anms-template/anms-spec-template.md` | ANMS仕様書テンプレート v0.33 |
| `prompt/review-handoff.md` | 元のタスク定義書（変更の本質テーブルあり） |
| `.claude/agents/` | 更新済みエージェント定義群 |
| `.claude/commands/full-auto-dev.md` | 更新済みコマンド定義 |

## マニュアルで変更した主要箇所

1. **ディレクトリ構成**（Ch4）: `anms-template/`, `spec/` 追加、`docs/srs/`, `docs/sws/` 削除
2. **Phase 1-2 ワークフロー**（Ch5）: SRS/SWS作成 → ANMS Ch1-2作成 / Ch3-6詳細化
3. **エージェント定義**（Ch6）: srs-writer, architect, review-agent のインライン定義を実ファイルと一致させた
4. **コマンド定義**（Ch7）: full-auto-dev.md のインライン定義を実ファイルと一致させた
5. **レビュー観点表**（Ch5, Ch6）: R1→ANMS Ch1-2、R2/R4/R5→ANMS Ch3-4
6. **FAIL時のルーティング**（Ch5）: SRS/SWS差し戻し → ANMS仕様書の該当Chapterへの差し戻し
7. **Mermaidダイアグラム**（7箇所）: 全ノード・ラベルをANMS用語に更新
8. **CLAUDE.mdテンプレート**（Ch4）: SRS/SWS参照 → ANMS仕様書参照
9. **トレーサビリティ**（Ch9）: SRS要件ID → ANMS FR-xxx/NFR-xxx
10. **用語集・参照**（全体）: 100箇所以上の SRS/SWS → ANMS 用語置換

## 注意点

- マニュアルは MCBSMD 形式（6バッククォートで囲んだ単一Markdownブロック）で出力する規約だが、現在のファイルは通常Markdownで書かれている。ユーザーの意図に合わせること
- Mermaid図のラベルルール: 全矢印にラベル必須、flowchart/graphではパイプ記法 `-->|Label|`
- 数式はLaTeX記法（インライン `$...$`、ブロック `$$...$$`）
