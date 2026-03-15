# 整合チェック報告: 論文・テンプレート ↔ ルール文書

**レビュー日:** 2026-03-15
**対象:** 論文4本 + spec-template 2本 ↔ process-rules + document-rules
**適用観点:** G（論文 ↔ ルール整合）、H（spec-template ↔ ルール整合）

---

## 観点G: 論文 ↔ ルール整合

### G-1: ANMS/ANPS/ANGSの定義・説明がルール文書と一致するか

**OK** -- 論文（日英とも）の三段階テーブルとCLAUDE.mdの「仕様形式の選択」テーブルは一致している。

| 項目 | 論文 | CLAUDE.md | 一致 |
|------|------|-----------|:----:|
| ANMS: 単一Markdownファイル / 1コンテキストウィンドウ | Yes | Yes | OK |
| ANPS: 複数Markdownファイル + Common Block / 中規模 | Yes | Yes | OK |
| ANGS: GraphDB + Git（MDはビュー）/ 大規模 | Yes | Yes | OK |

document-rules 1条にも「ANMS / ANPS / ANGSにかかわらず本文書の文書管理フォーマットは適用される」とあり、整合している。

---

### G-2: ブロック構造の名称が一致するか（旧名称が残っていないか）

**OK** -- 論文中では以下のブロック名称が使われている。

- ANMS論文（日英）: 「Common Block」「Form Block」を ANPS概要で言及（L197/ja, L197/en）。旧名称（Type Block, Body等）は使用されていない。
- ANGS論文（日英）: 「Common Block」を ANPS説明で言及（L57/ja, L57/en）。旧名称は使用されていない。

document-rules 4条のブロック構造定義（Common Block / Form Block / Detail Block / Footer）と矛盾なし。

---

### G-3: 論文中のフェーズモデル参照がルール側の7フェーズと矛盾しないか

**警告** -- ANMS論文のライフサイクル図（L77-88/ja）は8ステップのフロー（1.Input Requirements → 2.Clarification Interview → ... → 8.UAT）を示しているが、これはprocess-rulesの7フェーズ（Phase 0-6）と直接対応する構造ではない。ただしこれは「仕様書のライフサイクル」であり「開発フェーズ」ではないため、概念レベルでの矛盾はない。

- 論文は開発フェーズの具体的な番号（Phase 0-6）に言及していないため、直接的な矛盾は発生しない。
- ANMS論文はspec-foundationとspec-architectureの分割について言及しており（L197/ja: 「spec-foundation（Ch1-2: srs-writerが作成）と spec-architecture（Ch3-6: architectが詳細化）」）、これはprocess-rulesのPhase 1（Ch1-2）とPhase 3（Ch3-6）の分担と整合する。

**ただし、ANMS論文のSTFB図（L44-56/ja）の矢印構造はCh1→Ch2→Ch3→Ch4→Ch5と直列で、Ch1→Ch6が独立矢印。process-rulesのフェーズフロー図はPhase 0→1→2→3→4→5→6と直列。Ch5(Test Strategy)がフェーズ的にPhase 3(設計)で作成されるか、Phase 5(テスト)で作成されるかが曖昧だが、spec-templateはCh5をspec-architecture（Ch3-6）に含めておりPhase 3で作成される前提となっている。process-rules L728も「ANMS仕様書 Ch5 (Test Strategy) を定義する」とPhase 3で記述。整合している。**

---

### G-4: ANGS論文の「MDはビュー、本体はGraphDB」の原則がルール側でも正しく記述されているか

**警告** -- ANGS論文の4設計原則はprocess-rules本文中に直接記述されていない。process-rulesは現時点でANMSのみを参照しており、ANGSの運用ルール（GraphDB操作、ビュー生成等）は未定義。ただし、ANGS自体がドラフト（alpha版）であり、process-rulesがANGSの運用手順をまだ含んでいないことは意図的と推察される。

- CLAUDE.mdの「仕様形式の選択」テーブルにANGSは記載されている（「GraphDB + Git（MDはビュー）」）。これはANGS論文の原則と一致。
- document-rules 14条の設計根拠テーブルに「仕様形態と独立」の記載があり、ANGSとの共存が設計意図として明記されている。

**結論:** ANGSがドラフトである限り、process-rulesにANGS運用手順がないことは問題ではない。ANGS正式化時にprocess-rulesの拡張が必要になる。

---

### G-5: 日英ペアの論文で内容に大きな乖離がないか

#### ANMS論文（anms-essay-ja.md ↔ anms-essay-en.md）

**OK** -- 概念レベルで一致。章構成、STFB図、ライフサイクル図、比較テーブル、三段階体系テーブル、数式、Appendixすべて対応している。

**軽微な差異:**
- ja版のReferences番号に重複あり（番号2が2つ存在: L210-211）。en版は正しく2,3,4...と付番されている。
- ja版のAppendix BのMermaid図タイトルが「Emergency_Brake_Sequence」、en版は「Chauffeur_Mode_Sequence」と異なるが、図の内容は同一。

#### ANGS論文（angs-essay-ja.md ↔ angs-essay-en.md）

**OK** -- 概念レベルで一致。4設計原則、グラフスキーマ、認知操作、オーガナイザーフロー、プログラミング史の位置づけ、すべて対応している。

**軽微な差異:**
- 両版ともドラフト表記あり。日本語版: 「ドラフト(作成中のα版)」、英語版: 「Draft (alpha version, work in progress)」。一致。

---

## 観点H: spec-template ↔ ルール整合

### H-1: spec-templateのチャプター構成がprocess-rulesのフェーズ定義と整合するか

**OK** -- spec-template（日英）は冒頭でANPS分割を明示している。

- spec-foundation（Ch1-2: Foundation・Requirements）-- オーナー: srs-writer -- Phase 1で作成
- spec-architecture（Ch3-6: Architecture・Specification・Test Strategy・Design Principles）-- オーナー: architect -- Phase 3で作成

process-rules:
- L31: 「Phase 1: 企画 -- ヒアリングからANMS仕様書 Ch1-2 作成」
- L33: 「Phase 3: 設計 -- ANMS仕様書 Ch3-6 詳細化」
- L1417: srs-writerエージェントがCh1-2を作成
- L1466: architectエージェントがCh3-6を詳細化

**整合している。**

---

### H-2: spec-templateにCommon Blockテンプレートが含まれているか / document-rulesのCommon Block仕様と矛盾しないか

**OK（設計意図として問題なし）** -- spec-template自体にはCommon Blockテンプレートは含まれていない。spec-templateは「仕様書の中身（STFB構造のCh1-6）」のテンプレートであり、Common Blockは「文書管理のメタデータ」であるため、別レイヤーとして管理されている。

spec-template冒頭にANPS運用時の記述がある:
- ja版 L19: 「ANPSでは各ファイルにCommon Block + Form Blockを付与する（文書管理規則に従う）」
- en版 L19: 「Under ANPS, each file carries a Common Block + Form Block (per the document management rules)」

document-rules 4.2実例5でも「Common Block + Form Blockは『仕様書というファイルのメタデータ』。ANMSの章構成は『仕様の中身』。メタデータと中身は別レイヤー」と明記。

**矛盾なし。**

---

### H-3: spec-templateのfile_type参照がdocument-rules 7条に存在するか

**OK** -- spec-templateが参照する2つのfile_type:

| file_type | document-rules 7条 | 存在 |
|-----------|---------------------|:----:|
| spec-foundation | L583: 「仕様書 Ch1-2（Foundation・Requirements）」 | OK |
| spec-architecture | L584: 「仕様書 Ch3-6（Architecture・Specification・Test Strategy・Design Principles）」 | OK |

名前空間も一致: `spec-foundation:` (L700)、`spec-architecture:` (L701)

---

### H-4: spec-templateの日英版で構造的な差異がないか

**OK** -- 構造的に同一。

- Chapter Structure テーブル: 同一6章 + Appendix
- Section Structure: 全セクション番号が一致（1.1-1.9, 2.1-2.2, 3.1-3.6, 4.1+候補, 5, 6, Appendix）
- EARS構文パターンテーブル: 6パターンとも一致
- Gherkinテンプレート: 構造同一
- Ch6 Design Principles: 24原則すべて同一順序・同一識別名
- CA凡例: 色コード・Mermaid図とも同一
- Design Rationale テーブル: 同一判断項目
- References: 同一8件

**構造的な差異なし。**

---

### H-5: spec-templateで参照されている外部依存モデルがdocument-rules 7条と一致するか

**不整合** -- spec-template自体は外部依存モデル（hw-requirement-spec等）を直接参照していない。ただし、これは設計上の問題ではなく、外部依存モデルは条件付きプロセスとしてdocument-rules 7条に登録されており、spec-templateからの参照は不要（spec Ch3のArchitecture章で必要に応じて参照する設計）。

document-rules 7条に存在する外部依存モデル:
- hw-requirement-spec（L588）
- ai-requirement-spec（L589）
- framework-requirement-spec（L590）

**矛盾なし（参照関係は暗黙的だが、document-rulesの external-dependency-spec テンプレート L596-617 で「I/F定義: SW側のAdapter層の境界。ここがSW Spec Ch3と対応する」と対応関係が明記されている）。**

---

## 追加発見事項

### 不整合-1: document-rulesのフェーズ名テーブルにphase-dependency-selectionが欠落

**不整合: document-rules L640-645 ↔ process-rules L200-208**

document-rulesのフェーズ名テーブル（L640-645）には6フェーズしか列挙されていない:

```
phase-setup / phase-planning / phase-design / phase-implementation / phase-testing / phase-delivery
```

process-rulesのフェーズ名テーブル（L200-208）には7フェーズ定義されている:

```
phase-setup / phase-planning / phase-dependency-selection / phase-design / phase-implementation / phase-testing / phase-delivery
```

**`phase-dependency-selection`（Phase 2: 外部依存の選定）がdocument-rulesに欠落している。** これは条件付きフェーズであるため省略した可能性もあるが、process-rulesでは正式なフェーズとして定義されており、commissioned_byフィールドの値として使用される可能性がある。

---

### 不整合-2: ANMS論文のReferences番号重複

**不整合: anms-essay-ja.md L210-211**

References項目の番号が重複している:

```
2. ANGS (AI-Native Graph Spec) — ...
2. Mavin, A., et al. "EARS: ..."
```

正しくは2番と3番であるべき。en版（L210-217）では正しく付番されている。

---

### 警告-1: process-rulesのpipeline-state:phaseフィールドの値域

**警告: document-rules L770 ↔ process-rules L200-208**

document-rules 9.1条の pipeline-state:phase フィールドの値域が「0-5」と定義されている（L770）。しかしprocess-rulesでは7フェーズ（Phase 0-6）が定義されており、正しくは「0-6」であるべき。

同様に、progress:phase（L870）も「0-5」となっているが、「0-6」が正しい。

---

### 警告-2: process-rulesがANMSのみを参照している

**警告: process-rules全体**

process-rulesのすべてのフェーズ記述が「ANMS仕様書」を前提としている（例: L31「ANMS仕様書 Ch1-2 作成」、L33「ANMS仕様書 Ch3-6 詳細化」）。ANPSやANGSを採用した場合のプロセス差分（ファイル分割時のレビュー単位、GraphDB運用時のワークフロー等）が記述されていない。CLAUDE.mdの仕様形式テーブルで3段階が定義されているにもかかわらず、process-rulesがANMS専用の記述になっている点は、将来的に不整合を生む可能性がある。

---

## サマリー

| カテゴリ | 件数 |
|---------|:----:|
| 不整合 | 3 |
| 警告 | 3 |
| OK | 9 |

### 不整合一覧

| ID | 箇所 | 内容 |
|----|------|------|
| G-5a | anms-essay-ja.md L210-211 | References番号の重複（2が2つ）。en版は正しい |
| H-add1 | document-rules L640-645 | phase-dependency-selection がフェーズ名テーブルに欠落。process-rules L204 では定義済み |
| H-add1b | document-rules L770, L870 | pipeline-state:phase / progress:phase の値域が「0-5」だが、7フェーズ体系では「0-6」が正しい |

### 警告一覧

| ID | 箇所 | 内容 |
|----|------|------|
| G-3 | ANMS論文全体 | 論文のライフサイクルとprocess-rulesの7フェーズの対応が暗黙的。明示的マッピングはないが矛盾もない |
| G-4 | process-rules全体 | ANGS運用手順が未記述。ANGSがドラフトのため現時点では意図的 |
| H-add2 | process-rules全体 | ANPS/ANGS採用時のプロセス差分が未記述。ANMS前提の記述のみ |
