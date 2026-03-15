# 用語集

> **本文書の位置づけ:** full-auto-dev フレームワークで使用する用語の定義。辞書的な一般用語は含まない。フレームワーク固有の意味・選定理由・非採用の代替を記録する。
> **関連文書:** [プロセス規則](full-auto-dev-process-rules-ja.md)、[文書管理規則](full-auto-dev-document-rules-ja.md)、[エージェント一覧](agent-list-ja.md)

---

## 1. 意図的に選定した用語

同義語が複数ある中で、意図的に1つを選んだ用語。非採用の代替とその理由を記録する。

| 用語 | English | 定義 | 非採用 | 理由 |
|------|---------|------|--------|------|
| 要求 | requirement | システムが満たすべき条件。EARS 構文で形式化する | 要件 | 要求が根源（求めるもの）。要件は派生（満たすべき条件）。Requirements = 要求で統一 |
| インタビュー | interview | ユーザーへの構造化質問による要求抽出 | ヒアリング | hearing は和製英語。英語では法廷審問・聴覚の意味 |
| status | ステータス | ワークフロー上の現在位置を示す値 | state | state（存在モード）と status（進捗位置）の区別は実務上不要。status に統一 |
| defect | 障害 | テスト・運用で発見された不具合の正式記録 | bug, バグ | defect = 正式記録（file_type）。bug はメトリクスの俗称としても使わない。defect に統一 |
| interview-record | インタビュー記録 | ユーザーインタビューの構造化記録（file_type） | hearing-record | 上記 interview の選定に連動 |
| disaster-recovery-plan | 災害復旧計画 | RPO/RTO に基づく復旧手順の定義（file_type） | dr-plan | 名前空間の略称禁止ルールに従う |

## 2. フレームワーク固有概念

辞書に載っていない、このフレームワークで定義される概念。

| 用語 | 定義 |
|------|------|
| STFB | Stable Top, Flexible Bottom（上剛下柔）。安定依存の原則に基づく仕様書の章構成。上位章は安定・抽象、下位章は可変・具体 |
| ANMS | AI-Native Minimal Spec。単一 Markdown ファイルの仕様書形式。1コンテキストウィンドウに収まる規模向け |
| ANPS | AI-Native Plural Spec。複数 Markdown ファイル + Common Block の仕様書形式。中規模向け |
| ANGS | AI-Native Graph Spec。GraphDB + Git の仕様書形式。大規模向け。MD はビュー |
| Common Block | 全 file_type 共通のメタデータブロック。ファイルの身元証明（識別・状態・ワークフロー・コンテキスト・出自） |
| Form Block | file_type 固有の構造化フィールドブロック。エージェントがパースして判断・アクションに使う |
| Detail Block | 詳細説明ゾーン。ドメイン知識の本体。人間とエージェントが理解のために読む |
| Footer | 更新履歴ブロック。append-only。監査用 |
| In | エージェントの入力。仕事開始時に存在するファイル。イミュータブル（読むだけ） |
| Out | エージェントの出力。仕事終了時の最終成果物。End Conditions に対応。次のエージェントの In になる |
| Work | エージェントの作業用一時ファイル。Out 完成後に削除する。再利用しない |

## 3. 略称の許可判定

名前空間（file_type 名）での略称使用の判定記録。原則: 略称禁止（document-rules §7）。

| 略称 | 正式名 | 判定 | 理由 |
|------|--------|:----:|------|
| WBS | Work Breakdown Structure | 許可 | PM の一般用語。「Work Breakdown Structure」と書く人はいない |
| SRS | Software Requirements Specification | エージェント名のみ許可 | `srs-writer` はエージェント名（名前空間ルール対象外）。名前空間には使用不可 |
| DR | Disaster Recovery | 不許可 | `disaster-recovery-plan` に改名済み。3語で上限内 |
| CR | Change Request | 不許可 | フィールド名 `change_request_status` に改名済み |
| HW | Hardware | 許可 | file_type `hw-requirement-spec` で使用。`hardware-requirement-spec` は4語で上限超過 |
| AI | Artificial Intelligence | 許可 | 一般用語。`ai-requirement-spec` で使用 |
| FW | Framework | 不許可 | `framework-requirement-spec` は3語で上限内。略称不要 |

## 4. 紛らわしい対の区別

似ているが異なる概念の区別を明確にする。

| 対 | 区別 |
|---|------|
| 要求 vs 変更要求 | 要求 = requirement（システムが満たすべき条件）。変更要求 = change request（仕様承認後のユーザー起点の変更リクエスト）。同じ「要求」だが英語では requirement vs request で別語 |
| 仕様書 vs テンプレート | 仕様書 = プロジェクト固有の成果物（docs/spec/）。テンプレート = フレームワークが提供する雛形（process-rules/spec-template-ja.md） |
| エージェント vs サブエージェント | エージェント = agent-list に登録された11のロール定義。サブエージェント = Claude Code が起動する子プロセス（エージェントを含む） |
| lead vs organizer | lead = プロセス規則で定義されたオーケストレーターエージェント。organizer = ANGS 論文で提案されたグラフ走査エージェント。現時点では同一の役割を異なる文脈で呼んだもの |
| document_status vs {type}_status | 同じ status。document_status = Common Block（文書ライフサイクル: draft/review/approved/archived）。{type}_status = Form Block（ドメイン固有のワークフロー位置） |
