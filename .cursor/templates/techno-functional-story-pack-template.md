# Techno-functional story pack — Sprint {{SPRINT_NUMBER}} — {{FEATURE_OR_SLICE}}

## Pack metadata

| Field | Value |
|--------|--------|
| Sprint | {{SPRINT_NUMBER}} |
| Feature / slice | {{FEATURE_OR_SLICE}} |
| Business rule sources | {{BUSINESS_RULE_SOURCE_PATHS}} |
| Generated | {{ISO_TIMESTAMP}} |
| Generator | `/generate-user-story` |
| Validation summary | {{VALIDATION_SUMMARY}} |

## Sprint summary

- **Business outcomes**: …
- **In-scope stories**: …
- **Cross-story dependencies**: …
- **Deferred / out of scope**: …

---

## Story {{STORY_ID}} — {{TITLE}}

### Gherkin (INVEST)

#### Story
As a …
I want …
So that …

#### Scenario: Happy path
```gherkin
Given …
When …
Then …
```

#### Scenario: Validation / error
```gherkin
Given …
When …
Then …
```

#### Acceptance criteria
- [ ] …

#### INVEST scorecard
| Dimension | Pass | Notes |
|-----------|------|-------|
| Independent | Y/N | … |
| Negotiable | Y/N | … |
| Valuable | Y/N | … |
| Estimable | Y/N | … |
| Small | Y/N | … |
| Testable | Y/N | … |

#### Rule traceability
| Scenario | Business rule ID | Source reference |
|----------|------------------|------------------|
| … | BR-… | … |

---

### Techno-functional — DDL hints

| Table / object | Action | Columns / constraints | Migration hint | Notes |
|----------------|--------|----------------------|----------------|-------|
| {{table}} | CREATE / ALTER | … | `migrations/…` or net-new | Story {{STORY_ID}} |

- **Existing baseline**: cite `migrations/**/*.sql` objects already present.
- **Destructive change**: only if story AC explicitly authorizes; cite AC quote + ID.

---

### Techno-functional — API surface

| Resource / operation | Method | Path or operation | Request shape (summary) | Response shape (summary) | Error cases |
|---------------------|--------|-------------------|-------------------------|--------------------------|-------------|
| … | GET/PATCH/POST | `/…` | … | … | 400, 409, … |

- **Auth**: …
- **Envelope**: project default / none / cite wrapper doc
- **Pagination / filters**: …

---

### Techno-functional — UI data workflow

| Screen / route | User steps | Fields / controls | Validation UX | Loading / empty / error states |
|----------------|------------|-------------------|---------------|--------------------------------|
| `/…` | 1. … 2. … | … | … | … |

- **UX reference**: Figma / spec path or `TBD` + gap ID
- **Roles**: …

---

### Techno-functional — Data tables (binding)

| UI field / control | API field | Persistence column | Transform / validation | Source |
|--------------------|-----------|-------------------|------------------------|--------|
| … | … | … | … | story / rule / mapping |

---

### Wiring sequence preview (for planner)

1. **Step 1**: Method + path — purpose — inputs/outputs
2. **Step 2**: … (depends on step 1 `id`)

---

### Gaps and clarifications (story {{STORY_ID}})

| Gap ID | Dimension | Missing | Question |
|--------|-----------|---------|----------|
| G-{{STORY_ID}}-01 | … | … | … |

---

*(Repeat story block for each story in the sprint pack.)*

## Pack-level dependency and clarification list

- …

## Downstream handoff

- **Planner input**: `@story_artifact: docs/sprint{{SPRINT_NUMBER}}/userstory/<feature>-techno-functional-pack.md`
- **Per-story plans**: `.cursor/plans/sprint{{SPRINT_NUMBER}}/<story-id>-governed-implementation-plan.md`
- **Sprint rollup**: `.cursor/plans/sprint{{SPRINT_NUMBER}}/sprint{{SPRINT_NUMBER}}-unified-governed-plan.md`
