# Claude Code ほぼ全自動ソフトウェア開発テンプレート

Claude Code のサブエージェント機能とカスタムコマンドを活用して、ソフトウェア開発プロセスを**ほぼ全自動化**するためのプロジェクトテンプレートです。

## 概要

「ほぼ全自動」とは、人間（ユーザー）の作業を以下の **3つのみ** に絞り込み、残りすべてを Claude Code およびそのサブエージェント群に委任するアプローチです。

| # | ユーザーが担当する作業 |
|---|----------------------|
| 1 | ソフトウェアのコンセプト・要件の提示 |
| 2 | 開発中の重要な判断（分岐点での意思決定） |
| 3 | 完成物の受入テスト（確認と承認） |

## クイックスタート

### 1. このリポジトリをテンプレートとして使う

```bash
git clone https://github.com/your-username/claude-code-full-auto-dev.git my-project
cd my-project
```

### 2. `spec.md` に要件を記述する

```markdown
# プロジェクト仕様: タスク管理アプリ

## 解決したい課題
チームの作業が属人化しており、誰が何をしているかわからない

## ターゲットユーザー
5〜20人規模の開発チーム

## 主要機能
- タスクの作成・担当者割当・期限設定
- ステータス管理（未着手/進行中/完了）
- チームメンバーへの通知

## 技術的制約
- 言語: TypeScript
- フレームワーク: Next.js
- データベース: PostgreSQL

## 品質要求
- レスポンスタイム: 200ms以内（API）
- 同時接続: 100ユーザー
- 可用性: 99.9%
```

### 3. `CLAUDE.md` のプロジェクト情報を更新する

`CLAUDE.md` の `[プロジェクト名]` や技術スタックのプレースホルダーを実際の値に置き換えてください。

### 4. 全自動開発を開始する

Claude Code を起動し、カスタムコマンドを実行します。

```
/full-auto-dev
```

## ディレクトリ構成

```
.
├── CLAUDE.md                    # Claude Code への指示（プロジェクト設定）
├── spec.md                      # プロジェクト仕様（ユーザーが記述）
├── claude-code-full-auto-dev-manual.md  # 詳細マニュアル
├── .claude/
│   ├── agents/                  # サブエージェント定義
│   │   ├── srs-writer.md        # 要求仕様書作成エージェント
│   │   ├── architect.md         # ソフトウェア設計エージェント
│   │   ├── security-reviewer.md # セキュリティレビューエージェント
│   │   ├── test-engineer.md     # テスト作成・実行エージェント
│   │   ├── review-agent.md      # 品質レビューエージェント
│   │   ├── progress-monitor.md  # 進捗管理エージェント
│   │   ├── change-manager.md    # 変更管理エージェント
│   │   ├── risk-manager.md      # リスク管理エージェント
│   │   └── license-checker.md   # ライセンス確認エージェント
│   ├── commands/                # カスタムコマンド定義
│   │   ├── full-auto-dev.md     # /full-auto-dev コマンド
│   │   ├── check-progress.md    # /check-progress コマンド
│   │   └── retrospective.md     # /retrospective コマンド
│   └── settings.json
├── docs/                        # 全成果物（自動生成）
│   ├── srs/                     # 要求仕様書
│   ├── sws/                     # ソフトウェア仕様書
│   ├── api/                     # OpenAPI仕様
│   ├── security/                # セキュリティ設計
│   ├── reviews/                 # レビュー報告
│   ├── progress/                # 進捗レポート・WBS
│   ├── risk/                    # リスク台帳
│   ├── decisions/               # 重要判断の監査記録
│   └── traceability/            # 要件↔設計↔テストのトレーサビリティ
├── src/                         # 実装コード（自動生成）
├── tests/                       # テストコード（自動生成）
└── infra/                       # IaC・マイグレーション（自動生成）
```

## カスタムコマンド

| コマンド | 説明 |
|---------|------|
| `/full-auto-dev` | `spec.md` を読み込み、Phase 0〜5 の全自動開発を開始する |
| `/check-progress` | 現在の開発進捗（WBS・バグカーブ・カバレッジ）を報告する |
| `/retrospective` | スプリント/フェーズのふりかえりを実施する |

## 開発フェーズ

```
Phase 0: 条件付きプロセス評価
         └─ 法規・特許・機能安全・アクセシビリティの要否判断

Phase 1: 企画
         └─ SRS（要求仕様書）作成 → ユーザー承認

Phase 2: 設計
         └─ SWS・OpenAPI仕様・セキュリティ設計・WBS・リスク台帳

Phase 3: 実装
         └─ コード実装（git worktree 並列）・単体テスト・セキュリティスキャン

Phase 4: テスト
         └─ 結合テスト・性能テスト・テスト消化曲線・バグカーブ更新

Phase 5: 納品
         └─ 最終レビュー・コンテナビルド・デプロイ・受入テスト手順書
```

各フェーズには **review-agent による品質ゲート** が設けられており、Critical/High 指摘がゼロになるまで次フェーズへの移行がブロックされます。

## エージェント構成

| エージェント | モデル | 役割 |
|------------|-------|------|
| srs-writer | Opus | IEEE 830準拠の要求仕様書（SRS）を作成 |
| architect | Opus | IEEE 1016準拠のソフトウェア仕様書（SWS）とOpenAPI仕様を作成 |
| security-reviewer | Opus | OWASP Top 10 準拠のセキュリティ設計・脆弱性レビュー |
| test-engineer | Sonnet | テスト作成・実行・カバレッジ計測・性能テスト（k6） |
| review-agent | Opus | R1〜R6観点（要件品質・設計原則・コーディング品質・並行性・パフォーマンス・テスト品質）でレビュー |
| progress-monitor | Sonnet | WBS管理・バグカーブ監視・コスト追跡 |
| change-manager | - | SRS承認後の変更要求管理 |
| risk-manager | - | リスク台帳の作成・更新・軽減策管理 |
| license-checker | - | 依存ライブラリのOSSライセンス確認 |

## 品質基準

| 項目 | 目標値 |
|------|-------|
| テストカバレッジ | 80% 以上 |
| 単体テスト合格率 | 95% 以上 |
| 結合テスト合格率 | 100% |
| Critical/High 指摘 | 0件（次フェーズ移行条件） |
| セキュリティスキャン Critical/High | 0件 |

## 前提条件

- [Claude Code](https://claude.ai/code) がインストール済みであること
- Claude Pro / Team / Enterprise サブスクリプション、または Anthropic API アカウント

## 詳細マニュアル

全機能の詳細な説明、チュートリアル、トラブルシューティングは [claude-code-full-auto-dev-manual.md](claude-code-full-auto-dev-manual.md) を参照してください。

## ライセンス

[LICENSE](LICENSE) を参照してください。
