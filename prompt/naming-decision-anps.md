## Naming Decision: ANPS (AI-Native Plural Spec)

**Decided: 2026-03-14**

### Three-Level Spec Hierarchy

| Level | Abbreviation | Full Name | Representation | Scale |
|-------|-------------|-----------|----------------|-------|
| 1 | ANMS | AI-Native Minimal Spec | Single Markdown file | Fits in one context window |
| 2 | ANPS | AI-Native Plural Spec | Multiple Markdown files + Common Block + pipeline-state | Does not fit in one context window, no GraphDB needed |
| 3 | ANGS | AI-Native Graph Spec | GraphDB + Git (MD is a view) | Large-scale |

### Naming Rationale

- Minimal (1) -> Plural (many) -> Graph (structured): clear, simple progression
- "Plural" is universally understood (basic English grammar: singular/plural)
- No abbreviation collision: ANMS / ANPS / ANGS are all distinct
- ANPS Document Management Rules = the operational foundation for level 2

### TODO: Update Existing Documents

The following files reference the old naming and need to be updated:

- [x] anms-template/anms-essay-en.md -- Done (2026-03-15): Abstract + Scaling section + References
- [x] anms-template/anms-essay-ja.md -- Done (2026-03-15): Abstract + Scaling section + References
- [x] anms-template/angs-essay-en.md -- Done (2026-03-15): Abstract + §1.1 + §1.4 table
- [x] anms-template/angs-essay-ja.md -- Done (2026-03-15): Abstract + §1.1 + §1.4 table
- [x] anms-template/anms-spec-template-en.md -- Done (2026-03-15): ANPS usage note after STFB intro
- [x] anms-template/anms-spec-template-ja.md -- Done (2026-03-15): ANPS usage note after STFB intro
- [x] claude-code-full-auto-dev-manual.md -- Renamed to process-rules/. Updated (2026-03-15)
- [x] CLAUDE.md template -- Done (2026-03-15): 仕様形式の選択セクション追加
- [x] README.md -- Done (2026-03-15): 3段階体系テーブル、ディレクトリ構成更新、ヒアリング反映、運用規則参照先更新
