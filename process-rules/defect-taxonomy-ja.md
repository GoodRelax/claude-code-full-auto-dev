``````markdown
# Defect Taxonomy — 不具合系用語の体系整理

## 1. 背景と目的

ソフトウェア開発において「不具合」を表す用語は多数存在し、文脈によって意味が異なる。本文書は IEEE 1044、IEC 61508、ISO 26262、ITIL を参照し、full-auto-dev フレームワークで使用する不具合系用語を一意に定義する。

**設計原則:** 日本語の「障害」「不具合」「故障」は多義的であるため、本フレームワークでは**英単語をそのまま使用**し、曖昧さを排除する。

---

## 2. 因果連鎖モデル（IEEE 1044 / IEC 61508）

**因果連鎖:**

```mermaid
flowchart LR
    Error["Error<br/>人間の誤り"]
    Fault["Fault<br/>潜在する不正状態"]
    Failure["Failure<br/>要求不充足の顕在化"]
    Incident["Incident<br/>本番サービス影響"]
    Hazard["Hazard<br/>安全上の危険源"]

    Error -->|"原因"| Fault
    Fault -->|"実行時に発現"| Failure
    Failure -->|"本番環境で影響"| Incident
    Failure -->|"人命_財産に<br/>害を及ぼす条件"| Hazard
```

Error が Fault を生み、Fault が発現して Failure になり、本番で Failure がサービスに影響すると Incident になる。Failure が人命・財産に害を及ぼす条件を持つとき、それを Hazard と呼ぶ（機能安全の文脈）。

---

## 3. 用語定義

### 3.1 因果連鎖上の用語（技術概念）

| 用語 | 参照規格 | 定義 | 具体例 |
|------|---------|------|--------|
| **Error** | IEEE 1044 | 人間の認識・判断・操作のミス。Fault の原因 | 配列の境界条件を誤認して off-by-one のコードを書いた |
| **Fault** | IEEE 1044, IEC 61508 | Error の結果、コード・設計・仕様に埋め込まれた不正状態。潜在的であり、特定の実行条件を満たすまで発現しない | `if (i <= array.length)` という境界条件の誤り（コードに存在するが、まだ実行されていない） |
| **Failure** | IEEE 1044, IEC 61508 | Fault が実行時に発現し、システムが要求（機能要求または非機能要求）を満たさなくなった事象 | 上記の off-by-one が実行され、ArrayIndexOutOfBoundsException が発生した |

### 3.2 Fault Origin（混入フェーズによる分類）

Fault は混入したフェーズによって3つに分類される。Defect の root cause analysis で fault origin を特定することで、修正すべき対象（仕様か、設計か、コードか）が決まる。

| 用語 | 参照規格 | 定義 | 具体例 |
|------|---------|------|--------|
| **Requirements Fault** | IEEE 1044 | 要求・仕様に混入した fault。仕様自体が間違っている、または不足している | 「ログインは3回失敗でロック」の要求なのに仕様書に記載されていない。または「5回」と誤記 |
| **Design Fault** | IEEE 1044 | 設計に混入した fault。仕様は正しいが設計が間違っている | 仕様は正しいがシーケンス図でロック判定をDB層でなくUI層に配置してしまった |
| **Implementation Fault** | IEEE 1044 | 実装に混入した fault（= coding fault）。設計は正しいがコードが間違っている | 設計は正しいが `failCount >= 3` を `failCount > 3` と書いた |

**Fault Origin と修正対象の対応:**

| Fault Origin | 修正対象 | 影響範囲 |
|-------------|---------|---------|
| Requirements Fault | 仕様書 Ch1-2（spec-foundation） | 設計・実装・テスト全てに波及。最もコストが高い |
| Design Fault | 仕様書 Ch3-4（spec-architecture） | 実装・テストに波及 |
| Implementation Fault | ソースコード（src/） | テストに波及。最もコストが低い |

### 3.3 Systematic Fault vs Random Hardware Fault（IEC 61508）

IEC 61508 では fault を決定論的なものと確率論的なものに大別する。

| 用語 | 参照規格 | 定義 | SW に存在するか |
|------|---------|------|:---------------:|
| **Systematic Fault** | IEC 61508 | 人間の error に起因する決定論的 fault。requirements / design / implementation の全てを含む。同じ条件で必ず再現する | Yes |
| **Random Hardware Fault** | IEC 61508 | HW の物理的劣化（経年、放射線等）に起因する確率論的 fault。確率的にしか発現しない | No（SW 固有の概念ではない） |

**SW のすべての fault は systematic fault である。** SW は物理的に劣化しないため、random hardware fault は存在しない。つまり SW の fault は必ず人間の error に起因し、同じ入力と状態で必ず再現する。これは「再現できない SW の不具合は、条件の特定が不十分なだけ」ということを意味する。

### 3.4 記録・管理上の用語（プロセス概念）

| 用語 | 参照規格 | 定義 | file_type | フェーズ |
|------|---------|------|-----------|---------|
| **Defect** | IEEE 1044 | テストまたは運用で発見された Failure（または Fault）の正式記録。再現手順・重大度・根本原因・修正内容を含む | `defect` | testing, implementation |
| **Incident** | ITIL, ISO 20000 | 本番環境で発生した計画外のサービス中断または品質低下事象。検知→調査→軽減→解決→事後分析の一連を記録する | `incident-report` | operation |

### 3.3 機能安全固有の用語

| 用語 | 参照規格 | 定義 | 前提条件 |
|------|---------|------|---------|
| **Hazard** | IEC 61508, ISO 26262 | Failure が人命・身体・財産・環境に害を及ぼしうる危険源。Hazard 自体はまだ事故（Accident）ではない | 条件付きプロセス「機能安全（HARA/FMEA）」が有効な場合のみ |
| **Risk（安全）** | IEC 61508 | Hazard の発生確率 × 暴露 × 制御可能性。プロジェクトリスク（file_type: risk）とは異なる概念 | 同上 |
| **ASIL** | ISO 26262 | Automotive Safety Integrity Level。Risk 評価に基づく安全度水準（A〜D）。車載以外では SIL（IEC 61508）を使用 | 同上 |
| **HARA** | ISO 26262 | Hazard Analysis and Risk Assessment。Hazard の特定と Risk 評価を行う分析手法 | 同上 |
| **FMEA** | IEC 60812 | Failure Mode and Effects Analysis。Fault の発生モードとその影響を体系的に分析する手法 | 同上 |

---

## 4. 因果連鎖の詳細図

**Error から Accident までの完全な因果連鎖（fault origin 分類付き）:**

```mermaid
flowchart TD
    subgraph Origin["原因層"]
        Error["Error<br/>人間の誤り<br/>認識_判断_操作のミス"]
    end

    subgraph Latent["潜在層（全て Systematic Fault）"]
        ReqFault["Requirements Fault<br/>仕様に混入した fault"]
        DesFault["Design Fault<br/>設計に混入した fault"]
        ImpFault["Implementation Fault<br/>コードに混入した fault"]
    end

    subgraph Manifest["顕在層"]
        Failure["Failure<br/>要求を満たさなくなった事象<br/>テスト中または本番で発現"]
    end

    subgraph Record["記録層"]
        Defect["Defect<br/>正式な問題記録<br/>file_type: defect"]
        IncidentRec["Incident<br/>本番サービス影響記録<br/>file_type: incident-report"]
    end

    subgraph Safety["機能安全層"]
        Hazard["Hazard<br/>人命_財産への<br/>危険源"]
        Accident["Accident<br/>実際の被害発生"]
    end

    Error -->|"仕様作成時に混入"| ReqFault
    Error -->|"設計時に混入"| DesFault
    Error -->|"コーディング時に混入"| ImpFault
    ReqFault -->|"実行条件を満たすと発現"| Failure
    DesFault -->|"実行条件を満たすと発現"| Failure
    ImpFault -->|"実行条件を満たすと発現"| Failure
    Failure -->|"テスト中に発見"| Defect
    Failure -->|"本番で発生"| IncidentRec
    Failure -->|"安全関連の場合"| Hazard
    Hazard -->|"防護策が不十分な場合"| Accident

    style Origin fill:#FFE0B2,stroke:#333,color:#000
    style Latent fill:#FFF9C4,stroke:#333,color:#000
    style Manifest fill:#FFCDD2,stroke:#333,color:#000
    style Record fill:#C8E6C9,stroke:#333,color:#000
    style Safety fill:#E1BEE7,stroke:#333,color:#000
```

因果連鎖は5層で構成される。橙（原因）→ 黄（潜在）→ 赤（顕在）→ 緑（記録）→ 紫（安全）。潜在層の fault は全て systematic fault（人間の error に起因する決定論的 fault）であり、fault origin によって requirements / design / implementation の3種に分類される。通常の SW 開発では原因〜記録層を扱い、機能安全が有効な場合のみ安全層が加わる。

---

## 5. 紛らわしい対の区別

### 5.1 Fault vs Defect

| 観点 | Fault | Defect |
|------|-------|--------|
| 性質 | 技術的状態（潜在） | 管理的記録（文書） |
| 存在場所 | コード・設計の中 | project-records/defects/ |
| 可視性 | テストで発見されるまで見えない | 起票された時点で可視 |
| 関係 | Fault が発見されて起票されると… | → Defect になる |

### 5.2 Failure vs Incident

| 観点 | Failure | Incident |
|------|---------|----------|
| スコープ | 技術的事象（テスト中でも本番でも） | 運用的事象（本番環境のみ） |
| 発生場所 | テスト環境、開発環境、本番環境 | 本番環境のみ |
| 記録先 | テスト中 → Defect、本番 → Incident | file_type: incident-report |
| 関係 | 全ての Incident は Failure だが… | テスト中の Failure は Incident ではない |

### 5.3 Defect vs Incident

| 観点 | Defect | Incident |
|------|--------|----------|
| フェーズ | implementation, testing | operation |
| owner | test-engineer | lead |
| 目的 | Fault の修正と再発防止 | サービス復旧と事後分析 |
| file_type | `defect` (DEF-NNN) | `incident-report` (INC-NNN) |
| 関係 | Incident の根本原因調査で新たな Defect が起票されることがある |

### 5.4 Hazard vs Risk（プロジェクト）

| 観点 | Hazard | Risk（file_type: risk） |
|------|--------|------------------------|
| 参照規格 | IEC 61508, ISO 26262 | PMBOK, CMMI-RSKM |
| 対象 | 人命・財産・環境への危害 | プロジェクト目標（スケジュール・コスト・品質）への影響 |
| 評価式 | 発生確率 × 暴露 × 制御可能性 | 発生確率 × 影響度（スコア 1-9） |
| 適用条件 | 条件付きプロセス「機能安全」が有効な場合のみ | 全プロジェクト（必須プロセス） |
| 記録先 | project-records/safety/ | project-records/risks/ |

---

## 6. フレームワークでの用語使い分けルール

### 6.1 基本ルール

1. **英単語をそのまま使う。** Error, Fault, Failure, Defect, Incident, Hazard は日本語に訳さない
2. **「障害」「不具合」「故障」「バグ」は使わない。** 多義的な日本語を排除し、英単語で一意に指す
3. **文脈で意味が決まるのではなく、用語で意味が決まる。** 「障害対応」ではなく「Incident 対応」、「障害票」ではなく「Defect 票」

### 6.2 file_type との対応

| 用語 | file_type | ID形式 | 記録先 |
|------|-----------|--------|--------|
| Defect | `defect` | DEF-NNN | project-records/defects/ |
| Incident | `incident-report` | INC-NNN | project-records/incidents/ |
| Hazard | （HARA/FMEA の分析結果として project-records/safety/ に記録） | — | project-records/safety/ |

### 6.3 Defect の根本原因分析で使う用語

Defect の Detail Block に根本原因を記載する際、因果連鎖の用語を使って構造化する。

```markdown
## Root Cause Analysis

- **Failure**: ログイン時に 500 エラーが返却される
- **Fault**: `auth-service.ts:42` で null チェックが欠落
- **Fault Origin**: implementation fault（設計には null ガード指示あり。コーディング時の見落とし）
- **Error**: 開発者がOAuth応答の nullable フィールドを見落とした
- **修正**: null チェックを追加し、未認証時は 401 を返却する
- **再発防止**: OAuth 応答型に strict null check を適用する lint ルールを追加
```

上記のように、Failure（何が起きたか）→ Fault（コードのどこが悪いか）→ Fault Origin（仕様/設計/実装のどこで混入したか）→ Error（なぜそうなったか）の順に記載することで、修正対象の特定と再発防止策の精度が上がる。

**Fault Origin による修正判断:**

| Fault Origin | 修正すべき成果物 | review-agent の観点 |
|-------------|-----------------|-------------------|
| Requirements Fault | spec-foundation（Ch1-2）→ 以降の全成果物に波及 | R1（要求品質） |
| Design Fault | spec-architecture（Ch3-4）→ 実装・テストに波及 | R2（設計原則） |
| Implementation Fault | src/（ソースコード）→ テストに波及 | R3（コーディング品質） |

---

## 7. 機能安全が有効な場合の追加用語

条件付きプロセス「機能安全（HARA/FMEA）」が有効な場合のみ、以下の用語が追加される。

**HARA/FMEA の分析フロー:**

```mermaid
flowchart TD
    Identify["Hazard 特定<br/>システムのFailureモードを列挙"]
    Assess["Risk 評価<br/>発生確率 x 暴露 x 制御可能性"]
    Classify["安全度水準の決定<br/>SIL_IEC61508 または ASIL_ISO26262"]
    Derive["安全要求の導出<br/>各Hazardに対する防護策"]
    Verify["安全要求の検証<br/>テスト_分析_レビュー"]

    Identify -->|"Hazard一覧"| Assess
    Assess -->|"Risk評価結果"| Classify
    Classify -->|"SIL/ASIL割当"| Derive
    Derive -->|"安全要求"| Verify
```

HARA は Hazard の特定（Identify）から安全要求の導出（Derive）までの分析プロセス。FMEA は Fault のモード分析に特化し、HARA の入力として使われることが多い。

| 用語 | 定義 | 使用場面 |
|------|------|---------|
| **Safety Goal** | Hazard に対する最上位の安全要求。violation すると Accident に至る | HARA の出力 |
| **Safety Requirement** | Safety Goal を実現するための具体的な要求。仕様書 Ch2 の NFR に追加される | 仕様書・設計 |
| **Safe State** | Hazard が存在しない、または許容可能な Risk レベルにあるシステム状態 | 設計・テスト |
| **FTTI** | Fault Tolerant Time Interval。Fault 発生から Hazard に至るまでの許容時間 | 設計 |

---

## 8. 用語集（glossary-ja.md）への反映

本章の内容は [glossary-ja.md](glossary-ja.md) に反映済み。

- §1 に error, fault, failure, defect（変更）, incident, hazard, fault origin を登録
- §4 に fault vs defect, failure vs incident, defect vs incident, hazard vs risk を登録

---

## 9. フレームワーク全文書への波及

「障害」→ 英単語への置換が必要な箇所の分類:

| 現在の表現 | 置換先 | 対象ファイル |
|-----------|--------|------------|
| 障害票 | defect 票 | process-rules, document-rules, CLAUDE.md |
| 障害カーブ | defect curve | process-rules, document-rules, full-auto-dev.md |
| 障害修正 | defect 修正 | CLAUDE.md |
| 障害対応手順 | incident 対応手順 | document-rules (runbook) |
| 障害オープン数 | defect open 数 | process-rules |
| 障害の重大度 | defect severity | document-rules |
| 障害ステータス | defect status | document-rules |
| 障害報告 | defect 報告 | document-rules |
| 障害検出 | defect 検出 | process-rules |
| インシデント記録 | incident report | document-rules, README |
| インシデント管理 | incident management | full-auto-dev.md |
``````
