# ほぼ全自動ソフトウェア開発テンプレート

[English version](README.md)

AIコーディングエージェントのマルチエージェント機能を活用して、ソフトウェア開発プロセスを**ほぼ全自動化**するプロジェクトテンプレートです。ユーザーがやることは、コンセプトの提示・重要判断・受入テストの3つだけ。

## AIプラットフォーム対応

デフォルトは **Claude Code**（Anthropic）向けですが、他のAIコーディングエージェントにも移植可能です。

| 対応状況 | プラットフォーム |
|---|---|
| そのまま使用可 | Claude Code |
| 移植ガイドあり | OpenAI Codex CLI, Gemini CLI, Cursor, Windsurf, Cline, Roo Code, Aider |

- ファイルの約70%はポータブル（成果物・コード・プロセス規則）— 変更不要
- 約15%は一括置換（ベンダー名・モデル名・ディレクトリパス）
- 約15%はフォーマット変換（エージェント定義のYAMLフロントマター）— プロンプト本文は流用可能

**対象AIに [`process-rules/porting-guide-ja.md`](process-rules/porting-guide-ja.md) を読ませて自動変換を指示してください。** この変換ができないAIに本フレームワークを使う能力はありません。

## 多言語対応

デフォルトは英語（`-en.md`）です。切り替え手順:

1. 不要な言語サフィックスのファイルを一括削除（例: `-ja.md`）
2. 残ったファイルをAIに対象言語へ一括翻訳させる（例: `-vi.md`）

言語サフィックスのないファイル（`CLAUDE.md`, `.claude/agents/*.md` 等）は翻訳不要です。

> 本リポジトリは英語・日本語を併用して開発しています。リリース前にAIエージェントが言語間の一致性を検証しています。

## クイックスタート

### Step 1. インストール

```bash
git clone https://github.com/your-username/claude-code-full-auto-dev.git my-project
cd my-project
```

### Step 2. AIプラットフォームの切り替え（Claude Code以外を使う場合）

対象AIで [`process-rules/porting-guide-ja.md`](process-rules/porting-guide-ja.md) を読み込み、自動変換を実行。

### Step 3. 言語の切り替え（英語以外を使う場合）

不要な言語のファイルを削除し、残ったファイルをAIに翻訳させる。

### Step 4. 全自動開発を開始する

`user-order.md` にコンセプトを書いて起動:

```
/full-auto-dev
```

AIがプロジェクト構成を提案し、構造化インタビューを実施し、仕様書を生成し、設計・実装・テスト・納品まで自動で進行します。

## ドキュメント

- [プロセス規則](process-rules/full-auto-dev-process-rules-ja.md) — フェーズ定義・エージェント・品質ゲート
- [文書管理規則](process-rules/full-auto-dev-document-rules-ja.md) v0.0.0 — 命名・ブロック構造・バージョニング
- [移植ガイド](process-rules/porting-guide-ja.md) — 他AIプラットフォームへの変換仕様
- [仕様形式・設計根拠](essays/) — ANMS / ANPS / ANGS 三段階仕様体系

## ライセンス

[LICENSE](LICENSE) を参照。
