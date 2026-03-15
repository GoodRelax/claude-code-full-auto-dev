``````markdown
# Project Consistency Review -- God's Eye View

**Date:** 2026-03-15
**Reviewer:** Claude Opus 4.6 (review-agent role)
**Scope:** All formal documents in the repository
**Verdict:** FAIL (Critical: 2, High: 7, Medium: 5, Low: 2)

---

## 1. Directory Structure Consistency

### 1.1 Actual filesystem vs README.md directory tree

**Filesystem:**

```text
.
CLAUDE.md
README.md
LICENSE
.claude/
  agents/ (9 files)
  commands/ (3 files)
  settings.json, settings.local.json
.mcp.json
docs/
  api/
  document-inventory.md
  observability/
  security/
spec/ (.gitkeep only)
src/ (.gitkeep only)
tests/ (.gitkeep only)
essays/ (4 essays + old/ + research/)
process-rules/ (4 files + old/)
project-management/ (does NOT exist)
project-records/ (does NOT exist)
prompt/ (NOT in README)
sandbox/ (NOT in README)
old/ (NOT in README)
```

**Findings:**

| Item | README.md | Actual FS | Verdict |
|------|-----------|-----------|---------|
| `project-management/` | Listed | Does not exist | Expected (created at runtime) |
| `project-records/` | Listed | Does not exist | Expected (created at runtime) |
| `docs/spec/` | Listed | Does not exist (only `spec/.gitkeep` at root) | FAIL -- see Finding F-01 |
| `user-order.md` | Listed at root | Does not exist | Expected (user creates) |
| `executive-dashboard.md` | Listed at root | Does not exist | Expected (created at runtime) |
| `final-report.md` | Listed at root | Does not exist | Expected (created at runtime) |
| `infra/` | Listed | Does not exist | Expected (created at runtime) |
| `prompt/` | Not listed | Exists | OK (working drafts, not formal) |
| `sandbox/` | Not listed | Exists | OK (experimental, not formal) |
| `old/` | Not listed | Exists | OK (archive) |
| `docs/document-inventory.md` | Not listed | Exists | Minor omission |

### 1.2 README.md vs Document Rules SS2

README uses `docs/spec/` throughout. Document Rules SS2 also uses `docs/spec/`. These are consistent with each other.

### 1.3 README.md vs Process Rules SS2.5

Process Rules SS2.5 uses `docs/spec/` as well. Consistent.

**However**, the root-level `spec/` directory (with `.gitkeep`) exists as a stale artifact. This conflicts with the documented path `docs/spec/`.

---

## 2. Path Reference Consistency

### F-01 [Critical] Agent definitions reference `spec/` instead of `docs/spec/`

All 4 agents that reference the spec directory use the bare `spec/` path:

| File | Line | Reference |
|------|------|-----------|
| `.claude/agents/srs-writer.md` | 14 | `spec/ に作成します` |
| `.claude/agents/srs-writer.md` | 26 | `spec/[project-name]-spec.md として出力する` |
| `.claude/agents/architect.md` | 14 | `spec/ の ANMS 仕様書` |
| `.claude/agents/architect.md` | 17 | `spec/ の ANMS 仕様書を読み込む` |
| `.claude/agents/architect.md` | 53 | `spec/[project-name]-spec.md` |
| `.claude/agents/security-reviewer.md` | 18 | `spec/ の ANMS 仕様書 Ch2` |
| `.claude/agents/test-engineer.md` | 18 | `spec/ の ANMS 仕様書` |

CLAUDE.md, process-rules, document-rules all define the correct path as `docs/spec/`. The agent definitions contradict this. At runtime, agents will write to the wrong location or fail to find the spec.

**Impact:** Agents will create/read specs from `spec/` (root), not `docs/spec/` as defined by all rule documents. This causes file-not-found errors or scattered outputs.

**Fix:** Change all `spec/` references in agent `.md` files to `docs/spec/`.

### F-02 [Critical] Agent definitions reference obsolete `docs/` subpaths

The following agents still use the old `docs/` prefix paths instead of the reorganized `project-management/` and `project-records/` paths:

| Agent | Old Path | Correct Path |
|-------|----------|-------------|
| `change-manager.md:16` | `docs/change-log/CR-{番号}.md` | `project-records/change-requests/change-request-NNN-*.md` |
| `change-manager.md:19` | `docs/change-log/change-log.md` | `project-records/change-requests/` |
| `license-checker.md:19` | `docs/license/license-report.md` | `project-records/licenses/license-report.md` |
| `progress-monitor.md:28` | `docs/progress/` | `project-management/progress/` |
| `review-agent.md:252` | `docs/reviews/review-{...}.md` | `project-records/reviews/review-NNN-*.md` |
| `risk-manager.md:34-35` | `docs/risk/risk-register.json` | `project-records/risks/risk-register.md` |
| `risk-manager.md:35` | `docs/risk/risk-report.md` | `project-records/risks/` |
| `test-engineer.md:35` | `docs/performance/perf-report-{日付}.md` | `project-records/performance/performance-report-NNN-*.md` |
| `test-engineer.md:40` | `docs/test-plans/test-plan.md` | `project-management/test-plan.md` |
| `test-engineer.md:41` | `docs/performance/perf-report-{日付}.md` | `project-records/performance/` |
| `test-engineer.md:42` | `docs/progress/test-progress.json` | `project-management/progress/test-progress.json` |
| `test-engineer.md:43` | `docs/progress/bug-report.json` | `project-management/progress/bug-curve.json` |

**Impact:** Every agent that writes process documents will put them in wrong locations. The 3-directory separation (docs / project-management / project-records) defined in document-rules SS2 and CLAUDE.md is completely broken at the agent level.

**Fix:** Rewrite all agent `.md` files to match document-rules SS2 paths.

### F-03 [High] Custom commands reference obsolete paths

| Command | Old Path | Correct Path |
|---------|----------|-------------|
| `check-progress.md:3` | `docs/progress/` | `project-management/progress/` |
| `retrospective.md:3` | `docs/defects/` | `project-records/defects/` |
| `retrospective.md:8` | `docs/improvement/retrospective-*.md` | `project-records/` (no `improvement/` in document-rules) |

**Fix:** Update command files to match document-rules paths.

### F-04 [High] README.md references `spec/` instead of `docs/spec/`

Lines 29, 31:

```text
spec/[project-name]-spec.md（ANMS形式の正式仕様書）
spec/[project-name]-spec.md（Ch3-6 詳細化済み）
```

Should be `docs/spec/[project-name]-spec.md`.

**Fix:** Update README.md lines 29 and 31.

### F-05 [High] Stale `anms-template/` references in `prompt/` directory

The following files under `prompt/` still reference the old `anms-template/` path (28 occurrences across 5 files):

- `prompt/document-rules-handoff.md`
- `prompt/angs-essay-review-handoff.md`
- `prompt/manual-finetune-handoff.md`
- `prompt/naming-decision-anps.md`
- `prompt/review-handoff.md`

While `prompt/` is a working-drafts directory (not formal), these files are used as handoff instructions and can mislead future agents.

**Fix:** Archive these to `prompt/old/` or update references to current paths (`essays/`, `process-rules/spec-template-*.md`).

### F-06 [Medium] `docs/document-inventory.md` references obsolete paths

Lines 279-288 list obsolete path mappings as known issues, which is good (self-aware). However, the document itself still exists and references old paths like `docs/reviews/`, `docs/progress/`, etc. as the current state, which can confuse agents reading it.

---

## 3. Terminology Consistency

### F-07 [High] `change-manager.md` uses `docs/change-log/` and `CR-{番号}` naming

Document-rules defines:
- Directory: `project-records/change-requests/`
- File type: `change-request`
- Naming: `change-request-NNN-YYYYMMDD-HHMMSS.md`
- Namespace: `change-request:`

But `change-manager.md` uses:
- Directory: `docs/change-log/`
- Naming: `CR-{番号}.md`
- Additional file: `change-log.md` (not defined in document-rules)

This is both a path and terminology inconsistency.

### F-08 [Medium] `risk-manager.md` outputs JSON instead of Markdown with Common Block

`risk-manager.md` line 34 specifies `docs/risk/risk-register.json`. Document-rules defines `risk-register.md` as a Markdown singleton with Common Block in `project-records/risks/`.

### F-09 [Low] `test-engineer.md` uses `bug-report.json` instead of `bug-curve.json`

Document-rules SS3.2 and process-rules both use `bug-curve.json`. The test-engineer agent uses `bug-report.json`.

### Form Block / Detail Block / user-order.md terminology

These terms are consistently used across all formal documents (document-rules, process-rules, CLAUDE.md, README.md, essays). No instances of the old terms "Type Block" or "Body" were found.

**Verdict:** PASS for terminology (Form Block / Detail Block / user-order.md).

---

## 4. CLAUDE.md vs Process Rules SS3.1 Template Consistency

### F-10 [High] Root CLAUDE.md has sections absent from SS3.1 template

| Section | Root CLAUDE.md | SS3.1 Template |
|---------|:-:|:-:|
| 言語設定 (detailed fields) | Present | Partial (2 lines only) |
| 仕様形式の選択 (ANMS/ANPS table) | Present | Absent |
| **命名は言霊** in コーディング規約 | Present | Absent |
| Security scan output (`project-records/security/`) | Present | Absent |
| Agent Teams: Change Manager, Risk Manager, License Checker | Present (10 agents) | Absent (7 agents) |
| MCBSMD section | Present | Absent |

The SS3.1 template is described as "テンプレートの例示" with a note that "最新のテンプレートはリポジトリルートの `CLAUDE.md` を正本とすること". This mitigates the issue since the root CLAUDE.md is the source of truth. However, the template divergence means that SS3.1 serves as a misleading incomplete example.

**Fix:** Either (a) synchronize SS3.1 with root CLAUDE.md, or (b) add a prominent warning that SS3.1 is intentionally minimal and root CLAUDE.md contains the authoritative full template.

### F-11 [High] SS3.1 template lacks `process-rules/` reference in 開発方針

The root CLAUDE.md has:

```markdown
- 運用規則は以下を参照する:
  - process-rules/full-auto-dev-process-rules-ja.md（プロセス規則）
  - process-rules/full-auto-dev-document-rules-ja.md v0.0.0（文書管理規則）
```

SS3.1 template has:

```markdown
- 運用規則は以下を参照する:
  - process-rules/full-auto-dev-process-rules-ja.md（プロセス規則）
  - process-rules/full-auto-dev-document-rules-ja.md v0.0.0（文書管理規則）
```

These are actually the same. PASS on this specific point.

---

## 5. Document Rules Internal Consistency

### F-12 [High] SS13.3 says "全9ファイルタイプ" but SS7 defines 22 types

SS13.3 states:

> | 第7章の全9ファイルタイプ（Form Block付き） | ...

SS7 defines 22 file types (pipeline-state through user-order). The number "9" is stale from an earlier version.

**Fix:** Change "全9ファイルタイプ" to "全22ファイルタイプ".

### F-13 [Medium] SS9 defines Form Blocks for only 9 of 22 file types

SS9 (Form Block Specification) defines Form Blocks for:

1. pipeline-state (9.1)
2. handoff (9.2)
3. review (9.3)
4. decision (9.4)
5. risk (9.5)
6. progress (9.6)
7. defect (9.7)
8. change-request (9.8)
9. traceability (9.9)

Missing Form Block definitions for 13 file types:
- hearing-record, wbs, test-plan
- license-report, performance-report
- spec-foundation, spec-architecture
- threat-model, security-architecture, observability-design
- executive-dashboard, final-report, user-order

These 13 types are registered in SS7 with namespaces, and some are referenced in SS4.2 examples (wbs, executive-dashboard, final-report, user-order, performance-report), but they lack formal Form Block field definitions in SS9.

**Impact:** Agents creating these file types have no defined Form Block structure to follow, leading to inconsistent outputs.

**Fix:** Add SS9.10 through SS9.22 for all missing file types.

### SS5 Common Block fields

14 fields defined, template matches field list. Field order in template matches the documented order. PASS.

### SS7 vs SS8 namespace table

All 22 file types in SS7 have corresponding entries in SS8. Count: 22 `doc:` + 22 type-specific = consistent. PASS.

### SS7.1 workflow reference table

All 22 file types appear in the SS7.1 workflow reference table. PASS.

### SS9 section headings

All defined Form Blocks (9.1-9.9) follow the meta-template structure with `### Fields` + `### Detail Block Guidance`. PASS.

### SS11 ownership table

All 22 file types from SS7 are covered:
- pipeline-state, executive-dashboard, final-report, decision -> lead
- user-order, hearing-record, spec-foundation -> srs-writer
- spec-architecture, observability-design -> architect
- review -> review-agent
- progress, wbs -> progress-monitor
- risk -> risk-manager
- change-request -> change-manager
- test-plan, defect, traceability, performance-report -> test-engineer
- threat-model, security-architecture, sast-report -> security-reviewer
- license-report -> license-checker
- handoff -> from-agent

PASS.

### SS12 language policy vs Common Block `language` field

SS12 defines the 3-layer language model. Common Block `doc:language` field (SS5) uses ISO 639-1. These are consistent. PASS.

---

## 6. Essays and Templates Consistency

### Essays mention ANPS three-level hierarchy

| Essay | ANPS mentioned | Three-level table |
|-------|:-:|:-:|
| anms-essay-ja.md | Yes (line 7, 186, 195) | Yes |
| anms-essay-en.md | Yes (line 7, 186, 195) | Yes |
| angs-essay-ja.md | Yes (line 8, 22, 50, 57) | Yes |
| angs-essay-en.md | Yes (line 8, 22, 50, 57) | Yes |

PASS.

### spec-template ANPS usage notes

| Template | ANPS note |
|----------|:-:|
| spec-template-ja.md (line 14, 19) | Yes |
| spec-template-en.md (line 14, 19) | Yes |

PASS.

### English/Japanese parity

| Document pair | JA lines | EN lines | Match |
|--------------|----------|----------|:-----:|
| spec-template | 285 | 285 | PASS |
| anms-essay | 319 | 319 | PASS |
| angs-essay | 613 | 613 | PASS |

### F-14 [Medium] Missing English versions of rules documents

Document-rules SS2 lists:

```text
process-rules/
  full-auto-dev-process-rules-en.md
  full-auto-dev-document-rules-en.md
```

Neither file exists. SS12.5 defines that framework documents should be provided as ja/en pairs.

**Fix:** Create English versions, or remove references from SS2 and mark as TODO.

### F-15 [Medium] Process Rules SS2.5 lists directories not in Document Rules SS2

Process Rules SS2.5 includes directories absent from Document Rules SS2:

| Directory | In Process Rules SS2.5 | In Document Rules SS2 |
|-----------|:--:|:--:|
| `project-records/release/` | Yes | No |
| `project-records/improvement/` | Yes | No |
| `project-records/archive/` | Yes | No |
| `project-records/legal/` | Yes (conditional) | No |
| `project-records/safety/` | Yes (conditional) | No |
| `project-management/test-plan.md` | Yes | Yes (SS3.2) |
| `docs/observability/` | Yes | Yes (SS7) |

The `release/`, `improvement/`, `archive/` directories are described as "推奨" in process-rules but have no file_type registration in document-rules SS7 and no Form Block definition.

**Fix:** Either register these as file types in document-rules SS7, or explicitly document them as "unmanaged directories" in document-rules.

### F-16 [Low] `retrospective.md` command references `docs/improvement/` which is undefined

The retrospective command outputs to `docs/improvement/retrospective-*.md`. This path is not defined in either document-rules or the README directory tree (README only shows `project-records/` with no `improvement/` subdirectory).

---

## Summary Table

| ID | Severity | Category | Description |
|----|----------|----------|-------------|
| F-01 | **Critical** | Path | Agent definitions use `spec/` instead of `docs/spec/` |
| F-02 | **Critical** | Path | Agent definitions use obsolete `docs/` subpaths for process documents |
| F-03 | **High** | Path | Custom commands reference obsolete paths |
| F-04 | **High** | Path | README.md uses `spec/` instead of `docs/spec/` |
| F-05 | **High** | Path | `prompt/` files reference obsolete `anms-template/` |
| F-07 | **High** | Terminology + Path | `change-manager.md` uses obsolete naming and paths |
| F-10 | **High** | Template | Root CLAUDE.md diverges significantly from SS3.1 template |
| F-12 | **High** | Internal | SS13.3 "全9ファイルタイプ" should be 22 |
| F-06 | Medium | Path | `document-inventory.md` references obsolete paths |
| F-08 | Medium | Format | `risk-manager.md` outputs JSON instead of Markdown |
| F-13 | Medium | Internal | SS9 missing Form Block definitions for 13 of 22 types |
| F-14 | Medium | Parity | Missing English versions of rules documents |
| F-15 | Medium | Structure | Process Rules SS2.5 lists directories not in Document Rules SS2 |
| F-09 | Low | Naming | `test-engineer.md` uses `bug-report.json` instead of `bug-curve.json` |
| F-16 | Low | Path | `retrospective.md` references undefined `docs/improvement/` |

## Severity Summary

| Severity | Count |
|----------|-------|
| Critical | 2 |
| High | 6 |
| Medium | 5 |
| Low | 2 |

## Remediation Priority

### Immediate (blocks all project execution)

1. **F-01 + F-02:** Rewrite all 9 agent `.md` files to use correct paths (`docs/spec/`, `project-management/`, `project-records/`). This is the single most impactful fix -- without it, no agent will produce output in the correct location.

2. **F-03:** Update 2 command `.md` files (`check-progress.md`, `retrospective.md`).

3. **F-04:** Update README.md spec path references.

### Same-phase (before v1.0.0 release)

4. **F-12:** Fix SS13.3 count from 9 to 22.
5. **F-10:** Synchronize SS3.1 template with root CLAUDE.md, or add explicit "minimal example" disclaimer.
6. **F-07:** Rewrite `change-manager.md` entirely to match document-rules naming.
7. **F-13:** Define Form Blocks for the 13 missing file types in SS9.
8. **F-05:** Archive or update stale `prompt/` handoff files.

### Recommended (before v1.0.0)

9. **F-14:** Create English rule documents or defer with TODO marker.
10. **F-15:** Reconcile SS2.5 and SS2 directory lists.
11. **F-08:** Change risk-manager output from JSON to Markdown.
12. **F-09, F-16:** Fix minor naming inconsistencies.

## Pass/Fail Checklist

- [x] ANPS three-level hierarchy: All 4 essays reference it
- [x] Form Block / Detail Block terminology: Consistent across all formal docs
- [x] user-order.md naming: Consistent
- [x] spec-template ja/en parity: Line counts match
- [x] SS5 Common Block: 14 fields, template matches
- [x] SS7 vs SS8: All 22 types have namespaces
- [x] SS7.1 workflow table: All 22 types covered
- [x] SS9 meta-template structure: All defined blocks follow it
- [x] SS11 ownership: All 22 types covered
- [x] SS12 language policy: Consistent with `doc:language`
- [ ] Agent path references: FAIL (F-01, F-02)
- [ ] Command path references: FAIL (F-03)
- [ ] README path references: FAIL (F-04)
- [ ] SS13.3 file type count: FAIL (F-12)
- [ ] SS9 Form Block completeness: FAIL (F-13)
- [ ] Rules en/ja parity: FAIL (F-14)

## Overall Verdict

**FAIL** -- 2 Critical and 6 High findings.

The core design (document-rules, CLAUDE.md, process-rules) is internally consistent and well-structured. The three-directory separation, Common Block system, and ANPS hierarchy are coherently defined.

However, the **agent definitions and commands have not been updated** to reflect the directory reorganization from the old `docs/` monolithic structure to the new 3-directory model (`docs/` + `project-management/` + `project-records/`). This means that while the rules documents correctly define where files should go, the agents that actually create those files will put them in the wrong locations.

The root cause is clear: the rules documents were reorganized (likely recently), but the `.claude/agents/*.md` and `.claude/commands/*.md` files were not updated in the same pass.

**Recommended single action:** A batch update of all 9 agent files and 2 command files to replace old paths with the paths defined in document-rules SS2. This single action resolves F-01 through F-04 and F-07.
``````
