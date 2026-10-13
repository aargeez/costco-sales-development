# Test Results — {{SCOPE_TITLE}}

**Date:** {{YYYY-MM-DD}}  
**Iteration:** {{n}}  
**Environment:** {{OS, Java version, Node version, DB if IT ran}}

## Summary

| Suite | Run | Passed | Failed | Skipped | Status |
|-------|----:|-------:|-------:|--------:|--------|
| API unit (Surefire) | | | | | |
| API integration (Failsafe / `*IT`) | | | | | |
| UI unit (Vitest) | | | | | |
| UI E2E (Playwright) | | | | | |

**Overall:** {{passed}}/{{total}} — {{PASS | FAIL | BLOCKED}}

---

## Commands Executed

```bash
# API (Java 17) — config.yaml → evals.test_commands.api
export JAVA_HOME=...
# Example: cd <modules.api> && mvn test
# Integration: mvn verify -DrunIntegrationTests=true

# UI — evals.test_commands.ui / .e2e
# Example: cd <modules.ui> && npm test
# Example: cd <modules.ui> && npm run test:e2e
```

---

## API Unit Tests

| Class | Tests | Result |
|-------|------:|--------|

---

## API Integration Tests

| Test | Result | Notes |
|------|--------|-------|

---

## UI Unit Tests

| File | Tests | Result |
|------|------:|--------|

---

## UI E2E

(Summary or link to `artifacts/e2e-test-results-{timestamp}.md` if detailed log.)

---

## Coverage (if run)

| Metric | Value |
|--------|-------|
| API line (JaCoCo) | |
| API branch (JaCoCo) | |

**Output path:** `artifacts/test-results-{{date}}.md`
