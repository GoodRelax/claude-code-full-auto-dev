# Claude Code ほぼ全自動ソフトウェア開発テンプレート

Claude Code のサブエージェント機能とカスタムコマンドを活用して、ソフトウェア開発プロセスを**ほぼ全自動化**するためのプロジェクトテンプレートです。

## 概要

「ほぼ全自動」とは、人間（ユーザー）の作業を以下の **3つのみ** に絞り込み、残りすべてを Claude Code およびそのサブエージェント群に委任するアプローチです。

| # | ユーザーが担当する作業 |
|---|----------------------|
| 1 | ソフトウェアのコンセプト・要件の提示 |
| 2 | 開発中の重要な判断（分岐点での意思決定） |
| 3 | 完成物の受入テスト（確認と承認） |

## 仕様書フォーマット: ANMS (AI-Native Minimal Spec)

本テンプレートは **ANMS v0.33** を仕様書フォーマットとして採用しています。ANMSは EARS・Gherkin・Mermaid を **STFB（Stable Top, Flexible Bottom）** 構成で組み合わせた単一文書型の仕様書テンプレートです。

```
spec.md（3問形式: 何を？ どうして？ その他の希望）
    ↓ Phase 0: AI が CLAUDE.md を提案
    ↓ srs-writer エージェント + anms-template/
spec/[project-name]-spec.md（ANMS形式の正式仕様書）
    ↓ architect エージェント
spec/[project-name]-spec.md（Ch3-6 詳細化済み）
    ↓ 実装・テスト・レビュー
src/ + tests/（コード・テスト）
```

詳細は [anms-template/](anms-template/) を参照してください。

## クイックスタート

### 1. このリポジトリをテンプレートとして使う

```bash
git clone https://github.com/your-username/claude-code-full-auto-dev.git my-project
cd my-project
```

### 2. `spec.md` に3つの問いに答える

```markdown
# 作りたいもの

## 何を作りたい？
チームのタスクを管理できるWebアプリ。タスクの作成・担当者割当・期限設定ができて、ダッシュボードで進捗が見えるようにしたい。

## それはどうして？
チームの作業が属人化しており、誰が何をしているかわからない。Excelでの管理が限界。

## その他の希望
Webで使いたい。スマホからも確認できるとうれしい。
```

プロジェクト名・技術スタック・CLAUDE.md は Phase 0 で AI が提案するので、ユーザーが事前に用意する必要はありません。

### 3. 全自動開発を開始する

Claude Code を起動し、カスタムコマンドを実行します。

```
/full-auto-dev
```

Phase 0 で AI が CLAUDE.md を提案・配置した後、srs-writer エージェントが `spec.md` と `anms-template/anms-spec-template.md` を基に ANMS 形式の仕様書を `spec/` に生成し、以降のフェーズが自動で進行します。

## ディレクトリ構成

```
.
├── CLAUDE.md                    # Claude Code への指示（Phase 0でAIが提案）
├── spec.md                      # 作りたいもの（ユーザーが3問に回答）
├── claude-code-full-auto-dev-manual.md  # 詳細マニュアル
│
├── anms-template/               # ANMS テンプレート（日英）
│   ├── anms-spec-template.md    # テンプレ正本（JA）
│   ├── anms-spec-template-en.md # テンプレ EN
│   ├── anms-essay.md            # 論文正本（JA）
│   └── anms-essay-en.md         # 論文 EN
│
├── spec/                        # AIが生成する正式仕様書（ANMS形式）
│
├── .claude/
│   ├── agents/                  # サブエージェント定義
│   │   ├── srs-writer.md        # ANMS仕様書作成エージェント（Ch1-2）
│   │   ├── architect.md         # ANMS仕様書詳細化エージェント（Ch3-6）+ OpenAPI
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
├── docs/                        # 自動生成成果物
│   ├── api/                     # OpenAPI仕様
│   ├── security/                # セキュリティ設計
│   ├── reviews/                 # レビュー報告
│   ├── progress/                # 進捗レポート・WBS
│   ├── risk/                    # リスク台帳
│   ├── decisions/               # 重要判断の監査記録
│   └── traceability/            # 要件↔設計↔テストのトレーサビリティ
├── src/                         # 実装コード（自動生成）
├── tests/                       # テストコード（自動生成）
├── infra/                       # IaC・マイグレーション（自動生成）
└── old/                         # 旧版保管
```

## カスタムコマンド

| コマンド | 説明 |
|---------|------|
| `/full-auto-dev` | `spec.md` を読み込み、CLAUDE.md提案 → Phase 0〜5 の全自動開発を開始する |
| `/check-progress` | 現在の開発進捗（WBS・バグカーブ・カバレッジ）を報告する |
| `/retrospective` | スプリント/フェーズのふりかえりを実施する |

## 開発フェーズ

```
Phase 0: 条件付きプロセス評価
         └─ 法規・特許・機能安全・アクセシビリティの要否判断

Phase 1: 企画
         └─ ANMS仕様書 Ch1-2（Foundation・Requirements）作成 → ユーザー承認

Phase 2: 設計
         └─ ANMS仕様書 Ch3-6 詳細化・OpenAPI仕様・セキュリティ設計・WBS・リスク台帳

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
| srs-writer | Opus | ANMS形式の仕様書（Ch1-2: Foundation・Requirements）を作成 |
| architect | Opus | ANMS仕様書 Ch3-6 を詳細化し、OpenAPI仕様を作成 |
| security-reviewer | Opus | OWASP Top 10 準拠のセキュリティ設計・脆弱性レビュー |
| test-engineer | Sonnet | テスト作成・実行・カバレッジ計測・性能テスト（k6） |
| review-agent | Opus | R1〜R6観点（要件品質・設計原則・コーディング品質・並行性・パフォーマンス・テスト品質）でレビュー |
| progress-monitor | Sonnet | WBS管理・バグカーブ監視・コスト追跡 |
| change-manager | - | 仕様書承認後の変更要求管理 |
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
