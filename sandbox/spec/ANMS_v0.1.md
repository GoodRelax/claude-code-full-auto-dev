# ANMS v0.1 — Chapter & Section Structure (Draft)

## Overview

AI-Native Minimal Spec (ANMS) の章・セクション構成案。
本文書は章名とセクション名の確定を目的とする。

---

## Chapter Structure

| # | English | 日本語 | 主な記法 |
|---|---|---|---|
| 1 | **Foundation** | 基本事項 | 自然言語 + テーブル |
| 2 | **Requirements** | 要求 | EARS + 数式 + テーブル |
| 3 | **Architecture** | アーキテクチャ | Mermaid (CAレイヤー色分け必須) |
| 4 | **Specification** | 仕様 | テーブル + コードブロック |
| 5 | **Scenarios** | シナリオ | Gherkin |
| 6 | **Design Principles Compliance** | SW設計原則 準拠確認 | テーブル |
| A | **Appendix** | 付録 | 自由形式 |

---

## Section Structure

### Chapter 1. Foundation (基本事項)

プロジェクトの「北極星」。すべての後続章の前提となる。

| Section | English | 日本語 | 記述内容 |
|---|---|---|---|
| 1.1 | Background | 背景 | なぜこのSWが必要か。ドメインの現状 |
| 1.2 | Issues | 課題 | 現状の具体的な問題点 |
| 1.3 | Goals | 目標 | 成功の定義。達成すべき状態 |
| 1.4 | Approach | 解決方針 | 技術スタック、アーキテクチャ方針 |
| 1.5 | Scope | 範囲 | In-scope / Out-of-scope |
| 1.6 | Constraints | 制約事項 | 絶対に破れない物理的・技術的制約 |
| 1.7 | Limitations | 制限事項 | 既知の妥協点。「やるが完璧ではない」事項 |
| 1.8 | Glossary | 用語集 | プロジェクト固有の用語定義。AIとの語彙同期 |

### Chapter 2. Requirements (要求)

システムが常に満たすべき要求。EARS構文および数式で記述する。

| Section | English | 日本語 | 記述内容 |
|---|---|---|---|
| 2.1 | Functional Requirements | 機能要求 | EARS構文による機能要求の定義 |
| 2.2 | Non-Functional Requirements | 非機能要求 | 性能、セキュリティ、可用性等の要求 |

EARS構文パターン:

- **Ubiquitous:** The [System] shall [Response].
- **Event-driven:** When [Trigger], the [System] shall [Response].
- **State-driven:** While [In State], the [System] shall [Response].
- **Unwanted Behavior:** If [Trigger], then the [System] shall [Response].

数式による要求定義も許容する（暗号、信号処理等のドメイン）。

### Chapter 3. Architecture (アーキテクチャ)

SWの構造と設計判断。Mermaid図はCAレイヤー色分けを必須とする。

| Section | English | 日本語 | 記述内容 |
|---|---|---|---|
| 3.1 | Components | コンポーネント | 部品と責務の分割。コンポーネント図 |
| 3.2 | Domain Model | ドメインモデル | 構造・関係・状態の定義。クラス図、ER図、状態遷移図 |
| 3.3 | Behavior | 振る舞い | 処理フロー・相互作用。シーケンス図、アクティビティ図 |
| 3.4 | Decisions | 設計判断 | ADR（Architecture Decision Records）。判断理由・代替案・決定者 |

**CA レイヤー凡例 (コンポーネント図・クラス図 共通):**

```mermaid
graph RL
    subgraph Legend["CA Layer Legend"]
        direction RL

        L_F["Framework"]:::framework --> L_A["Adapter"]:::adapter
        L_A --> L_U["Use Case"]:::usecase
        L_U --> L_E["Entity"]:::entity
    end

    classDef entity fill:#FF8C00,stroke:#333,color:#000
    classDef usecase fill:#FFD700,stroke:#333,color:#000
    classDef adapter fill:#90EE90,stroke:#333,color:#000
    classDef framework fill:#87CEEB,stroke:#333,color:#000
```

| CAレイヤー | 役割 | 色 | Hex |
|---|---|---|---|
| Entity | ドメインデータ・コアロジック | 橙 | `#FF8C00` |
| Use Case | ビジネスロジック調整 | 金 | `#FFD700` |
| Adapter | 外部IF適合 | 緑 | `#90EE90` |
| Framework | UI・デバイス・外部サービス | 青 | `#87CEEB` |

### Chapter 4. Specification (仕様)

AIがコードに直接変換できるレベルの具体的定義。プロジェクトの性質に応じてセクションを取捨選択する。

| Section候補 | English | 日本語 | 適用場面 |
|---|---|---|---|
| 4.x | UI Elements Map | UI要素マップ | UIを持つアプリ |
| 4.x | Configuration | 設定定義 | 設定オブジェクトを持つアプリ |
| 4.x | API Definition | API定義 | Web API を提供するアプリ |
| 4.x | Data Schema | データスキーマ | DB を使用するアプリ |
| 4.x | State Management | 状態管理 | 複雑な状態遷移を持つアプリ |
| 4.x | Algorithm | アルゴリズム | 数理・暗号等の演算ロジック |
| 4.x | File Structure | ファイル構成 | 全アプリ共通（推奨） |
| 4.x | Error Handling | エラー処理 | エラー体系の定義が必要なアプリ |

### Chapter 5. Scenarios (シナリオ)

Gherkin形式による振る舞い定義。AIがテストコードを直接生成するための入力となる。

```gherkin
Feature: [機能名]
  Scenario: [シナリオ名]
    Given [前提条件]
    When [操作・イベント]
    Then [期待結果]
    And [追加の期待結果]
```

### Chapter 6. Design Principles Compliance (SW設計原則 準拠確認)

アーキテクチャおよび実装がSW設計原則に準拠しているかを確認する。Chapter 3-5 の「定義・設計・検証」とはメタレベルが異なる、品質保証の層。

| 原則 | 準拠状況 |
|---|---|
| SRP | [各クラス/モジュールの単一責務の説明] |
| OCP | [拡張ポイントの説明] |
| DIP | [依存性逆転の適用箇所] |
| CQS | [コマンド/クエリ分離の説明] |
| POLA | [最小驚きの原則への準拠] |
| PIE | [意図の明確な命名・表現] |

プロジェクトの性質に応じて確認する原則を追加・削除してよい。

### Appendix (付録)

| Section | English | 日本語 | 記述内容 |
|---|---|---|---|
| A.1 | References | 参考文献 | 標準規格、外部資料へのリンク |
| A.2 | Licenses | ライセンス | 依存ライブラリのライセンス情報 |
| A.x | (その他) | (その他) | プロジェクト固有の補足資料 |

---

## Design Rationale (本構成の設計根拠)

| 判断 | 根拠 |
|---|---|
| SRS/SWS統合 → 1文書化 | AIのコンテキストウィンドウに全情報を入れるため。参照の切れ目がハルシネーションの温床 |
| EARS + 数式のハイブリッド | EARSだけでは数理仕様を表現できない。ドメインに応じて使い分け |
| Mermaid CAレイヤー色分け必須 | 人間のレビュー効率向上。レイヤー識別が一目で可能 |
| 色はgrsmd_gen2_specに準拠 | Entity(橙#FF8C00), UseCase(金#FFD700), Adapter(緑#90EE90), Framework(青#87CEEB) |
| ADRをArchitecture章内に配置 | 設計と根拠をセットで読める。Appendixに追いやると参照が切れる |
| Specification章はセクション候補制 | 全SW開発に適用するため。分野ごとに取捨選択 |
| Design Principles Complianceを独立章(Ch6)に | Ch3-5の「定義・設計・検証」とはメタレベルが異なる品質保証の層 |
| Limitations追加 | Scope(やらない)とConstraints(破れない)の間にある「妥協点」を明示 |
| Glossary追加 | AIとの語彙同期。grsmd_gen2_specで有効性を実証済み |
