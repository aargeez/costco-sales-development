# Validation Report: {project_name}

## Metadata
| Field | Value |
|-------|-------|
| Model | `{model_label}` |
| Project Path | `{project_path}` |
| Validation Date | `{timestamp}` |
| Iteration | `{iteration}` of {max_iterations} |
| TypeScript Status | `{pass \| fail}` |

---

## Score Summary

| Category | Score | Grade | Weight | Weighted |
|----------|-------|-------|--------|----------|
| Guidelines Adherence | {score}/10 | {grade} | 25% | {weighted} |
| Folder Structure | {score}/10 | {grade} | 15% | {weighted} |
| Maintainability | {score}/10 | {grade} | 15% | {weighted} |
| Reusability | {score}/10 | {grade} | 15% | {weighted} |
| Type Safety | {score}/10 | {grade} | 10% | {weighted} |
| State Management | {score}/10 | {grade} | 10% | {weighted} |
| Code Quality | {score}/10 | {grade} | 10% | {weighted} |
| **Static Weighted** | **{static_score}/10** | **{grade}** | **100%** | |
| **LLM Overall** | **{llm_score}/10** | **{grade}** | — | |
| **Combined Final** | **{combined}/10** | **{grade}** | — | |

---

## Project Overview
| Metric | Value |
|--------|-------|
| Total Files | {total_files} |
| TypeScript (.ts) | {ts_files} |
| TSX (.tsx) | {tsx_files} |
| CSS Files | {css_files} |
| Lines of Code | {loc} |

---

## Findings by Severity

### Critical (MUST Fix)
| # | Category | Finding | Remediation |
|---|----------|---------|-------------|
| {n} | {category} | {finding} | {fix_instruction} |

### High (SHOULD Fix)
| # | Category | Finding | Remediation |
|---|----------|---------|-------------|
| {n} | {category} | {finding} | {fix_instruction} |

### Medium (NICE to Fix)
| # | Category | Finding | Remediation |
|---|----------|---------|-------------|
| {n} | {category} | {finding} | {fix_instruction} |

### Low (Optional)
| # | Category | Finding | Remediation |
|---|----------|---------|-------------|
| {n} | {category} | {finding} | {fix_instruction} |

---

## LLM Evaluation (if available)

### Rationale
| Category | Score | Assessment |
|----------|-------|------------|
| Architecture | {score}/10 | {rationale} |
| Component Design | {score}/10 | {rationale} |
| Figma Alignment | {score}/10 | {rationale} |
| Best Practices | {score}/10 | {rationale} |

### Strengths
{strengths_list}

### Improvements
{improvements_list}

---

## Refinement History
| Iteration | Score Before | Score After | Fixes Applied | Remaining |
|-----------|-------------|-------------|---------------|-----------|
| {n} | {before} | {after} | {fixes_count} | {remaining_count} |

---

## Acceptance Gate
- [ ] TypeScript compiles with zero errors
- [ ] Combined score >= 7.0
- [ ] Zero raw CSS files
- [ ] All components have Props interfaces
- [ ] Theme exists with palette + typography + overrides
- [ ] Barrel exports in components/index.ts
