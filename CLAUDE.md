# プロジェクト: [プロジェクト名]

## プロジェクト概要

[ユーザーが提示するコンセプトをここに記載]

## 開発方針

- 本プロジェクトはほぼ全自動開発で進行する
- ユーザーへの確認は重要判断のみに限定する
- 軽微な技術的判断はClaude Codeが自律的に行う
- ANMS形式の仕様書はspec/配下に出力する。その他の成果物はdocs/配下にMarkdownで出力する
- コードはsrc/配下、テストはtests/配下、IaCはinfra/配下に配置する

## 技術スタック

- 言語: [例: TypeScript]
- フレームワーク: [例: Next.js 15]
- データベース: [例: PostgreSQL]
- テストフレームワーク: [例: Vitest]
- 性能テスト: [例: k6]
- コンテナ: [例: Docker / docker-compose]
- IaC: [例: Terraform]
- CI/CD: [例: GitHub Actions]
- 可観測性: [例: OpenTelemetry + Grafana]

## ブランチ戦略

- メインブランチ: main（直接コミット禁止）
- 開発ブランチ: develop（統合ブランチ）
- 機能ブランチ: feature/{issue番号}-{説明}（develop から分岐）
- バグ修正ブランチ: fix/{issue番号}-{説明}
- リリースブランチ: release/v{バージョン}（develop から分岐）
- PRマージ: develop → main は review-agent PASS 後にのみ許可
- Agent Teams の並列実装: git worktree を使用し、各エージェントは専用ブランチで作業

## コーディング規約

- [プロジェクト固有のルール]
- ESLint設定に従う
- すべての公開関数にJSDocコメントを付与する
- エラーハンドリングは明示的に行う
- 構造化ログ（JSON形式）を使用する（console.logは禁止）
- **命名は言霊:** `type`, `data`, `info`, `value` 等の意味を持たない汎用語は禁止。名前は「それが何か」を一目で伝えること。何の種別かをドメインで限定する（例: `status` → `decision_status`）

## セキュリティ要件

- OWASP Top 10 への対策を必須とする
- 認証にはJWTを使用する
- 入力値は必ずバリデーションする
- SQLインジェクション対策としてパラメタライズドクエリを使用する
- SAST: CodeQL（GitHub Actions で自動実行）
- SCA: npm audit / Snyk（依存関係追加時に必ず実行）
- シークレットスキャン: git-secrets または truffleHog（コミット前フック）

## テスト方針

- カバレッジ目標: 80%以上
- 単体テスト: すべてのビジネスロジック（合格率95%以上）
- 結合テスト: API エンドポイント（合格率100%）
- E2Eテスト: 主要ユーザーフロー
- 性能テスト: 非機能要件の数値目標をk6で検証

## APIドキュメント

- OpenAPI 3.0形式で docs/api/ に出力する
- architect エージェントがANMS仕様書 Ch3 詳細化と同時に生成する
- 実装完了後 test-engineer がエンドポイントとの整合性を検証する

## 可観測性要件

- ログ: 構造化JSON形式、INFO/WARN/ERROR の3レベル
- メトリクス: RED（Rate/Error/Duration）メトリクスを全APIに計装
- トレーシング: OpenTelemetryでリクエスト追跡
- アラート: エラーレート1%超、P99レイテンシがSLA超過でアラート

## Agent Teams 設定

Agent Teamsで作業する場合、以下のロール定義を使用する:

- **SRS Agent**: spec.md（3問形式）+ anms-template/ を基に、ANMS形式の仕様書を spec/ に作成（Ch1-2 Foundation・Requirements）。ユーザーコンセプトを構造化する
- **Architect Agent**: spec/ の ANMS 仕様書 Ch3-6 を詳細化（Architecture・Specification・Test Strategy・Design Principles）。docs/api/ にOpenAPI仕様を生成する
- **Security Agent**: docs/security/ にセキュリティ設計を作成。実装コードの脆弱性レビューを行う
- **Implementation Agent**: src/ 配下にコードを実装する。設計文書に従う
- **Test Agent**: tests/ 配下にテストを作成・実行する。カバレッジレポートを生成する
- **Review Agent**: docs/reviews/ にレビュー報告を出力する。R1〜R6の観点（SW工学原則・並行性・パフォーマンス）でレビューし、Critical/High指摘がゼロになるまで次フェーズへの移行をブロックする
- **PM Agent**: docs/progress/ に進捗レポートを出力する。WBS/バグカーブ/コストを管理する

## 重要判断の基準

以下の場合はユーザーに確認を求めること:

- アーキテクチャに関する根本的な選択
- 外部サービス/APIの選定
- セキュリティモデルの重大な変更
- 予算やスケジュールに影響する判断
- 要件の曖昧さにより複数の解釈が可能な場合
- リスクスコア6以上のリスクが発生した場合
- コスト予算の80%に到達した場合
- 変更要求の影響度がHighの場合

以下の場合はClaude Codeが自律的に判断してよい:

- ライブラリの具体的なバージョン選定
- コードのリファクタリング方針
- テストケースの設計
- ドキュメントの構成
- バグ修正の方法

## 必須プロセス設定（第6章参照）

- 変更管理: 仕様書承認後の変更はchange-managerエージェント経由で処理する
- リスク管理: Phase 1完了時にリスク台帳を作成し、各フェーズ開始時に更新する
- トレーサビリティ: 要件ID→設計ID→テストIDの対応をdocs/traceability/に記録する
- 問題管理: バグはdocs/defects/に障害票として記録し、根本原因分析を行う
- ライセンス管理: 依存ライブラリ追加時にlicense-checkerエージェントを実行する
- 監査記録: 重要判断はdocs/decisions/に記録する
- コスト管理: APIトークン消費をdocs/progress/cost-log.jsonに記録する

## 条件付きプロセス（Phase 0で判断）

以下は該当する条件が存在する場合のみ有効化する:
- 法規調査: [有効/無効] - 理由: [記載]
- 特許調査: [有効/無効] - 理由: [記載]
- 技術動向調査: [有効/無効] - 理由: [記載]
- 機能安全(HARA/FMEA): [有効/無効] - 理由: [記載]
- アクセシビリティ(WCAG 2.1): [有効/無効] - 理由: [記載]

## ドキュメントの基本形式 (MCBSMD)

- Output the entire content **as a single Markdown code block** so it can be copied in one go.
- **Enclose the entire Markdown with six backticks ` `````` ` at the beginning and end.** Specify its language as markdown.
- **Use these six backticks only once as the outermost enclosure.**
- **Never output speculation or fabrications.** If something is unclear or requires investigation, explicitly state so.
- This method is called **MCBSMD** (Multiple Code Blocks in a Single Markdown)

### Code and Diagram Block Rules

- As a rule, use Mermaid for diagrams. Use PlantUML only when the diagram cannot be expressed in Mermaid.
- Any diagrams or software code inside the Markdown must each be enclosed in their own code blocks using triple backticks ` ``` `.
- Each code block must specify a language or file type (e.g., ` ```python ` or ` ```mermaid `).
- Each code or diagram block must be preceded by a descriptive title in the format **title:**
  (e.g., `**System Architecture:**`, `**Login Flow:**`)
- Always follow the structure below for every code or diagram block:

  > **title:**
  >
  > ```language
  > (code or diagram content here without truncation or abbreviation)
  > ```
  >
  > Write the explanation for the code block here, immediately after the block, following a blank line.

- Do not write explanations inside the code blocks.
- In all diagrams, use alphanumeric characters and underscores (`_`) by default; non-ASCII plain text (no spaces) is permitted when necessary. Special symbols (e.g., `\`, `/`, `|`, `<`, `>`, `{`, `}`) are strictly prohibited.
- Output all diagram content without omission. Never use `...` or any shorthand.

### Diagram Label and Notation Rules

- All arrows and relationship lines in diagrams MUST have labels. Follow these notation rules:
  1. Mermaid `flowchart` and `graph`: place the label inside the arrow using pipes (e.g., `A -->|Label| B`)
  2. Other Mermaid diagrams / All PlantUML: place the label after the arrow using a colon (e.g., `A --> B : Label`)
- For line breaks in labels or node text:
  1. Mermaid: use `<br/>` inside a quoted string (e.g., `A -->|"Line1<br/>Line2"| B`, `A["Line1<br/>Line2"]`)
  2. PlantUML: use `\n` (e.g., `A -> B : Line1\nLine2`)

### Math Rules

- Use standard LaTeX notation for all mathematical formulas.
  1. Inline math: always use single dollar signs. Place a space before the opening `$`
     and a space after the closing `$`
     (e.g., `The function is $y = x + 1$ here.`)
  2. Block equations: always place `$$` on its own line, above and below the formula.
     Example:
     > $$
     > E = mc^2
     > $$
