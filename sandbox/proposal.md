# プロセス改善提案

**日付:** 2026-03-05
**対象:** claude-code-full-auto-dev-manual.md
**ステータス:** 提案（未適用）

---

## 提案1: SRS/SWS分離の廃止 → Spec一本化

### 現状

- Phase 1: SRS (docs/srs/SRS-v1.0.md) を作成
- Phase 2: SWS (docs/sws/SWS-v1.0.md) を作成
- 2文書を参照しながら実装

### 問題点

1. AIのコンテキストウィンドウに2文書を入れる必要があり、参照の切れ目がハルシネーションの温床
2. 要件→設計のトレーサビリティが文書間をまたぐため追跡が困難
3. 記述コストが高い（重複する情報が発生）
4. grsmd_gen2_spec（実戦投入済み）は1文書統合型で成功している

### 提案

- SRS/SWSを統合し、ANMS形式の spec.md 1文書に統一
- Phase 1: spec.md を作成（Chapter 1-2 を中心に、Chapter 3-5 の骨格も含む）
- Phase 2: spec.md を肉付け（Chapter 3-5 を詳細化）+ レビュー&承認

### 影響範囲

- マニュアル 第7章 Phase 1-2 のフロー変更
- CLAUDE.md テンプレートの docs/srs/, docs/sws/ 参照を docs/spec/ に変更
- srs-writer, architect エージェント定義の統合または役割再定義
- review-agent のレビュー対象変更（SRS/SWS別 → spec.md の章別）

---

## 提案2: エージェントロールの再編

### 現状

- SRS Agent: docs/srs/ に要求仕様を作成
- Architect Agent: docs/sws/ にソフトウェア仕様を作成

### 提案

Spec一本化に伴い、以下のいずれかに再編：

**案A: Spec Agent に統合**
- 1つのエージェントが spec.md 全体を管理
- メリット: シンプル、文書内の整合性が保たれやすい
- デメリット: 1エージェントの負荷が大きい

**案B: 章別の責務分担を維持**
- SRS Agent → Chapter 1-2 担当（Foundation, Requirements）
- Architect Agent → Chapter 3-4 担当（Architecture, Specification）
- テスト Agent → Chapter 5 担当（Scenarios）
- レビュー Agent → Chapter 6 担当（Design Principles Compliance）
- メリット: 専門性が維持される
- デメリット: 同一ファイルへの並行書き込みの競合リスク

### 推奨

案B。ただし spec.md への書き込みは章単位でロックする運用ルールを追加。

---

## 提案3: Phase構成の簡略化

### 現状 (5 Phase)

- Phase 0: 条件付きプロセス評価
- Phase 1: 企画 — コンセプトからSRS作成
- Phase 2: 設計 — SWS・セキュリティ・WBS
- Phase 3: 実装
- Phase 4: テスト
- Phase 5: 納品

### 提案 (5 Phase、内容変更)

- Phase 0: 条件付きプロセス評価（変更なし）
- Phase 1: 仕様 — コンセプトから spec.md 作成（Ch1-5 + Ch6）
- Phase 2: 計画 — WBS・セキュリティ設計・リスク台帳・レビュー&承認
- Phase 3: 実装（変更なし）
- Phase 4: テスト（変更なし）
- Phase 5: 納品（変更なし）

### 変更点

- Phase 1 で SRS+SWS を統合した spec.md を一括作成
- Phase 2 は「計画と品質確認」に集中（spec.md はPhase 1で完成済み）
- レビューゲートは Phase 1 完了時に spec.md 全体を R1-R6 でレビュー

---

## 提案4: AI-人間間の議事録作成プロセスの導入

### 現状

- AI（Claude Code）と人間がざっくばらんに対話しながら仕様や設計を議論する
- 議論の内容はセッションのコンテキストに依存し、セッション終了後に散逸する
- 重要な判断の経緯や却下された代替案が記録に残らない

### 問題点

1. セッションが長くなるとコンテキスト圧縮により議論の詳細が失われる
2. 後日「なぜこの判断をしたか」を追跡できない
3. 複数セッションにまたがる議論の継続性が保証されない

### 提案

- AI-人間間の議論を要約した議事録を作成・保存するプロセスを追加する
- 議事録のフォーマットは別途検討（TBD）
- 保存先候補: docs/minutes/ または docs/decisions/

### 未決事項

- 議事録フォーマットの設計
- 自動生成 vs 手動トリガー
- 保存粒度（セッション単位 / トピック単位 / Phase単位）

---

## 適用手順（承認後）

1. ANMS テンプレートの最終確定（sandbox/spec/ANMS_v*.md）
2. claude-code-full-auto-dev-manual.md の Phase 1-2 を改訂
3. CLAUDE.md テンプレートの docs パス変更
4. エージェント定義の更新（.claude/agents/）
5. full-auto-dev コマンドの更新（.claude/commands/）
