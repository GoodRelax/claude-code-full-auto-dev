# 引継ぎプロンプト: 文書管理規則 設計セッション

## 前提

エージェント間コミュニケーション設計のブレストセッション（2026-03-14）の成果。
旧「マニュアル」を「プロセス規則」にリネームし、文書管理規則を新規策定した。

## セッションで確立した重要な概念的区別

### 仕様フォーマット vs 開発プロセス（混ぜるな危険）

| 軸 | 内容 | 例 |
|----|------|-----|
| 仕様フォーマット | 仕様書をどう書くか | ANMS / ANPS / ANGS |
| 開発プロセス | どう仕事するか | full-auto-dev（Phase 0-5、エージェント間文書管理） |

この2つは**直交する概念**。仕様規模やフォーマットが変わっても、プロセス（パイプライン管理、レビュー、引継ぎ等）は同じ。文書管理規則は**プロセス側**に属する。

### 三段階仕様体系（ANPS命名決定）

| レベル | 略称 | 正式名称 | 規模 |
|--------|------|----------|------|
| 1 | ANMS | AI-Native Minimal Spec | 1コンテキストウィンドウに収まる |
| 2 | ANPS | AI-Native Plural Spec | 収まらない、GraphDB不要 |
| 3 | ANGS | AI-Native Graph Spec | 大規模、GraphDB使用 |

詳細: `prompt/naming-decision-anps.md`（ANPS命名TODO残あり）

## 今回の成果物

### 新規作成

| ファイル | 内容 |
|---------|------|
| `process-rules/full-auto-dev-process-rules-ja.md` | 旧マニュアルをリネーム・冒頭更新 |
| `process-rules/full-auto-dev-document-rules-ja.md` | 文書管理規則 v1.0（全14章） |

### アーカイブ済（old/送り）

| 元ファイル | 移動先 |
|-----------|--------|
| `claude-code-full-auto-dev-manual.md` | `old/` |
| `prompt/doc-rule-draft.md` | `prompt/old/` |
| `prompt/documents-format.md` | `prompt/old/` |
| `prompt/pipeline-state-template.md` | `prompt/old/` |
| `prompt/type-block-designs.md` | `prompt/old/` |
| `prompt/ho.md` | `prompt/old/` |
| `prompt/full-auto-dev-document-rules-{ja,en}.md` | `prompt/old/`（ANPS名称版、廃止） |

## 文書管理規則の要点

### リポジトリ構成

```
{framework-root}/
  README.md                                # ルート唯一のドキュメント
  process-rules/                           # 運用規則（フレームワーク定義）
    full-auto-dev-process-rules-ja.md      # 旧マニュアル
    full-auto-dev-document-rules-ja.md     # 文書管理規則
  anms-template/                           # 仕様書テンプレート
  .claude/commands/                        # カスタムコマンド

{利用先project-root}/
  project-management/                      # オーケストレーション + PM成果物
  docs/                                    # 設計成果物（最終成果物）
  project-records/                         # プロセス記録（監査証跡）
```

### ブロック構造（Common Block + Type Block + Body + Footer）

- Common Block: doc: 名前空間。9フィールド。全ファイルタイプ共通
- Type Block: ファイルタイプ固有の名前空間（pipeline:, handoff:, review: 等）
- Body: 自由記述
- Footer: 更新履歴（change_log は append-only）

### 全9ファイルタイプ

| file_type | 名前空間 | ディレクトリ |
|-----------|---------|-------------|
| pipeline-state | `pipeline:` | `project-management/` |
| handoff | `handoff:` | `project-management/handoff/` |
| progress | `progress:` | `project-management/progress/` |
| review | `review:` | `project-records/reviews/` |
| decision | `decision:` | `project-records/decisions/` |
| risk | `risk:` | `project-records/risks/` |
| defect | `defect:` | `project-records/defects/` |
| change-request | `cr:` | `project-records/change-requests/` |
| traceability | `trace:` | `project-records/traceability/` |

### 命名規則（全カテゴリ網羅、第3章）

- 3.2 プロセス文書: `{file_type}-{NNN}-{YYYYMMDD}-{HHMMSS}.md`
- 3.3 プロセス記録: 同上
- 3.4 仕様書: `{project-name}-spec.md`（ANMS）/ `{project-name}-spec-ch{N}.md`（ANPS）
- 3.5 一般文書: 内容を表す名前
- 3.6 ソースコード: 言語規約に従う
- 3.7 テストコード: テストフレームワーク規約に従う
- 3.8 設定・インフラ: ツール標準名をそのまま使用

## 未着手タスク

### 高優先

1. **英語版作成**: `process-rules/full-auto-dev-document-rules-en.md`（文書管理規則）
2. **英語版作成**: `process-rules/full-auto-dev-process-rules-en.md`（プロセス規則。旧マニュアルの翻訳。約2660行）
3. **CLAUDE.mdテンプレート更新**: process-rules/ への参照パス追加。ディレクトリパス更新（docs/reviews/ → project-records/reviews/ 等）
4. **full-auto-dev.md コマンド更新**: 新ディレクトリ構成・文書管理規則への対応

### 中優先

5. **ANPS命名の既存文書反映**: `prompt/naming-decision-anps.md` のTODOリスト参照
6. **プロセス規則の内容精査**: 旧マニュアルの内容が新体制（process-rules/、3ディレクトリ構成）と整合しているか確認・修正
7. **README.md更新**: プロジェクト説明とディレクトリ構成の更新

### 低優先

8. **各Type Blockのフルテンプレート作成**: handoff以外（review, decision, risk, defect, cr, progress, traceability）のフルテンプレート例

## ユーザーからの重要フィードバック（次セッションでも守ること）

- **混ぜるな危険**: 仕様フォーマット（ANMS/ANPS/ANGS）とプロセス（full-auto-dev）は直交概念。混同しないこと
- **"records"はダメ**: 何のレコードか不明。`project-records` と明示する
- **progressはPMフォルダ**: 進捗管理はproject-management/配下
- **既存フォーマットに固執するな**: まだプロジェクトは始まっていない。全面改訂は今しかできない
- **マニュアルは重い**: ルートに重いファイルを置かない。ルートはREADME.mdだけ
- **git commit/push/tagは厳禁**: ユーザーが手動で行う
