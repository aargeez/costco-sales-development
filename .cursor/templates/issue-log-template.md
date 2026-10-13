# Issue Log — {{TITLE}}

| Field | Value |
|-------|-------|
| **ID** | ISS-{{YYYYMMDD}}-{{slug}} |
| **Reported** | {{YYYY-MM-DD}} |
| **Severity** | Critical / High / Medium / Low |
| **Classification** | backend / frontend / full-stack / config-env |
| **Classification rationale** | {{1–3 sentences — signals from /fix-issue Phase 0}} |
| **Agent(s)** | backend-agent (fix mode) / frontend-agent (fix mode) |
| **Status** | Triage / Plan pending / Approved / In progress / Fixed / Verified / Cancelled |

## Issue description

- **Summary:**
- **Steps to reproduce:**
- **Expected:**
- **Actual:**
- **Environment:** (local / branch / browser / JDK / DB)

## Root cause analysis

- **Where:** (file paths, layer — controller / service / UI component / config)
- **Why:** (mechanism — not symptom only)
- **BRD / contract reference:** (FR-xxx, OpenAPI operation, or DQ-xxx if silent)

## Impact analysis

| Layer | Affected | Risk |
|-------|----------|------|
| API / OpenAPI | | |
| Database / Flyway | | |
| UI / routes | | |
| Tests | | |
| Security / auth | | |

## Fix plan (pre-implementation)

1. 
2. 

**Approach:** Production-grade fix — reuse existing patterns; no workarounds.

**Human approval:** {{date}} — {{proceed | revise | cancelled}}  
**Approver note:**

## Implementation

- **Files changed:**
- **Design choices:** (what was reused vs extended)

## Test evidence

| Suite | Command | Result | Notes |
|-------|---------|--------|-------|
| Unit (new/changed) | | pass / fail | |
| API regression | `mvn test` | | |
| UI regression | `npm test` | | |
| E2E | Playwright | | |
| JaCoCo | `mvn jacoco:report` | | |

**Evidence files:** `artifacts/test-results-{{date}}.md`, `artifacts/e2e-test-results-*.md`

## Outcome

- **Fixed:** yes / no / partial
- **Regression risk:** low / medium / high
- **Open items / follow-ups:**
- **Recommended next command:** `/review-changes` | `/pr-assist` | `/plan-sprint`

## Sibling scan (required on `/fix-issue` defect closeout)

Search other modules in this sales application for the same anti-pattern. Module names come from `config.yaml` and the product layout. Do not hardcode a product. Record the result here. Do **not** edit those modules in this command.

Leave a row blank only when that label does not apply to this defect, and say why in Notes. A config-only fix with no shared pattern may mark every row **n/a** with that reason. An empty section is not a complete issue log.

| Label | Modules searched | Result | Notes |
|-------|------------------|--------|-------|
| Contract alias (OpenAPI name vs client name) | | found / not found / n/a | |
| Nav / bypass / `returnQs` | | found / not found / n/a | |
| Option column | | found / not found / n/a | |
| Seed clobber | | found / not found / n/a | |
| IT restore missing on shared tables | | found / not found / n/a | |
| Screen test without the module’s themed render helper | | found / not found / n/a | |
| Shared O/S profile filter missing | | found / not found / n/a | |

- **Other modules edited in this command:** no
- **Follow-up (separate issue, not this diff):**

**Output path:** `artifacts/issues/{{YYYY-MM-DD}}-{{slug}}.md`
