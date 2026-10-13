# Application Summary

Mirror section structure of the HTML named in `config.yaml` → `artifacts.application_summary_html`.

| Field | Value |
|-------|-------|
| **Generated** | {{date}} |
| **Output HTML** | `config.yaml` → `artifacts.application_summary_html` |
| **Output PDF** | `config.yaml` → `artifacts.application_summary_pdf` |

---

## 1. Application Description

(Bounded context, users, platform stack — API + UI + PostgreSQL.)

## 2. User Flows

### Primary entry flow
(Warehouse/session selection → review → actions — name the ATI/module from config / BRD)

### Summary / inquiry flow
(Session validation → summary or satellite queries as cited in BRD)

## 3. Application Flow

(Mermaid diagrams — source: `artifacts/diagrams/` when available.)

## 4. Business Logic Behind User Flows

| Rule area | Behavior | Where implemented |
|-----------|----------|-------------------|

## 5. Codebase Size (Lines of Code)

| Component | Production LOC | Test LOC |
|-----------|----------------|----------|
| API (Java) | | |
| UI (TypeScript/TSX) | | |
| Total | | |

## 6. Tests & Coverage

| Suite | Count | Scope |
|-------|-------|-------|
| API unit (JUnit) | | |
| API integration (`*IT`) | | |
| UI unit (Vitest) | | |
| E2E (Playwright + Chrome) | | |

### Coverage Summary

| Component | Line | Instruction | Branch |
|-----------|------|-------------|--------|

## 7. Key API Endpoints

| Method | Path | Purpose |
|--------|------|---------|

(Source: `config.yaml` → `modules.openapi`)
