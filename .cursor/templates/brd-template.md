# Business Requirements Document (BRD)
## {{Parent Application}} — {{Target Module Name}} ({{Legacy Module}} Replacement)

| Field | Value |
|---|---|
| **Document ID** | BRD-{{ID}} |
| **Version** | 1.0 |
| **Status** | Draft — Pending Domain Review |
| **Date** | {{YYYY-MM-DD}} |
| **Bounded Context** | {{context}} |
| **Legacy Module** | {{legacy}} |
| **Target Module Name** | {{target}} |
| **Primary Users** | {{users}} |
| **Target Platform** | {{platform}} |
| **Sources** | {{transcript dates}}, {{supporting doc list}} |

---

## 1. Executive Summary

(2–4 paragraphs: problem, replacement scope, platform, key integrations.)

---

## 2. Business Objectives

| # | Objective | Success Measure |
|---|---|---|

---

## 3. Scope

### 3.1 In Scope

### 3.2 Out of Scope

---

## 4. Stakeholders & Actors

| Actor | Role | Responsibilities |
|---|---|---|

---

## 5. Assumptions & Dependencies

| ID | Assumption / Dependency | Impact if Invalid |
|---|---|---|

---

## 6. User Journeys

### 6.1 Entry Flow (Happy Path)

```mermaid
flowchart TD
```

### 6.2 Reversal / Alternate Flows

```mermaid
flowchart TD
```

---

## 7. Functional Requirements

### 7.1 Authentication, Authorization & Navigation

| Req ID | Requirement | Priority | Source |
|---|---|---|---|

### 7.2 Screen 1 — Scan / Entry Point

| Req ID | Requirement | Priority | Source |
|---|---|---|---|

### 7.3 Item Identification & Barcode Parsing

| Req ID | Requirement | Priority | Source |
|---|---|---|---|

### 7.4 Validation Chain (Pre-Screen 2)

| Seq | Req ID | Validation | Error Message (en-US) | Error Message (fr-CA) | Data Source | Source |
|---|---|---|---|---|---|---|

### 7.5 Screen 2 — Transaction Detail

| Req ID | Requirement | Priority | Source |
|---|---|---|---|

### 7.6 Inquiry / list screens (required per in-scope legacy inquiry program)

Do **not** collapse a BRE **Present Logic** block (or Data dictionary subfile fields) into one sentence such as “shows tenders with amounts.” Split FRs (or numbered clauses on one FR) so each of the following is explicit. Do not put **row membership** on a sibling maintenance FR.

| Must capture | What to write | Typical source |
|--------------|---------------|----------------|
| **Row driver** | Legacy file + flag/filter that **creates** a subfile/API line | BRE Tables used **Input** “select/list”; DD key/select |
| **Optional joins** | Left-join files for adj/comments/etc. Missing join → blank/zero amount, **row still listed** | Tables used Input “retrieve amount” (not list) |
| **Empty condition** | When the grid is empty and the exact message (e.g. No records found) | BRE Condition / Action on the display rule |
| **Amount columns** | Each displayed amount cites `CALC-*` (or DQ if catalog gap) | Catalog; BRE Rule Type = Calculation; DD `Sum(...)` / `A+B` |
| **Trace** | One BRE rule with Present Logic → matching FR(s); do not map BR-n–BR-n+2 → a single vague FR | BRE Business Rules + §12 |

Copy this table once per inquiry program (replace placeholders):

| Req ID | Requirement | Priority | Source |
|---|---|---|---|
| FR-xxx | **Row driver:** list from `{{legacy_file}}` where `{{flag_or_filter}}` (exclude `{{codes}}` if stated). Not from `{{optional_join_file}}`. | Must | BRE Tables used {{program}} |
| FR-xxx | **Amounts:** {{column}} per `CALC-{{MODULE}}-…`; optional `{{join}}` does not drop the line. | Must | Catalog; BRE Present Logic |
| FR-xxx | **Empty:** {{message}} only when row-driver (+ vault/activity rule) has no rows — not when an optional join is absent. | Must | BRE display rule |

### 7.7–7.10

(Additional subsections per module — disposal, calculations, persistence, reversal, localization.)

---

## 8. Non-Functional Requirements

| Req ID | Category | Requirement |
|---|---|---|

---

## 9. Data Requirements

### 9.1 Input Data (Read)

For each in-scope program, every BRE **Tables used** Input row must appear here or in an FR: mark **list driver** vs **amount/validate** vs **header lookup**. Data dictionary display-file (subfile) fields are an FR, a `CALC-*`, or explicit Out of Scope.

### 9.2 Output Data (Write)

### 9.3 Configuration

---

## 10. Integration Requirements

| Int ID | From | To | Protocol | Purpose |
|---|---|---|---|---|

---

## 11. UI Specification

(Screens, fields, modes — reference mockup paths.)

---

## 12. Business Rules Traceability

| BR | Rule Summary | BRD Section |
|---|---|---|

---

## 13. Discussion Coverage Validation

| Discussion Topic | Covered In BRD | Notes |
|---|---|---|

**Template:** [discussion-coverage-matrix-template.md](discussion-coverage-matrix-template.md)

---

## 14. Open Questions & Risks

| ID | Type | Description | Owner | Blocking |
|---|---|---|---|---|

---

## 15. Acceptance Criteria (Module Level)

1. …

---

## 16. Test Strategy Summary

| Layer | Scope |
|---|---|

**Detailed test strategy:** `artifacts/sprints/<sprint-id>-test-strategy.md` (from `.cursor/templates/test-strategy-template.md`)

---

## 17. Behavior Provenance (Draft)

| behavior_id | statement | source_locator | row_driver | confidence |
|-------------|-----------|----------------|------------|------------|

**Full registry:** `.cursor/templates/behavior-provenance-template.md`

**Output path:** `artifacts/brd/<module>-BRD-draft.md`  
**Reference:** `config.yaml` → `modules.brd`
