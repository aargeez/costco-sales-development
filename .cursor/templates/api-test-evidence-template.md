# API Test Evidence Template

## Execution Metadata
- Date/Time:
- Environment:
- Host:
- Backend Commit/Version:
- Runner:

## Command Log
| Step | Command | Purpose | Exit Code | Notes |
|---|---|---|---:|---|
| 1 | `curl ... /v3/api-docs` | Verify API docs endpoint | 0 | - |
| 2 | `curl ... <rest-base-path>/<resource>?<db-param-name>=<primary-source>` | Validate REST list on primary source | 0 | - |
| 3 | `curl ... /graphql` | Validate GraphQL query | 0 | - |

## REST Evidence
| Endpoint | Request | Status | Key Response Snippet | Result |
|---|---|---:|---|---|
| `<rest-base-path>/<resource>` | GET `?<db-param-name>=<primary-source>` | 200 | `{"success":true,...}` | PASS/FAIL |

## GraphQL Evidence
| Operation | Request Variables | Status | Key Response Snippet | Result |
|---|---|---:|---|---|
| `<queryA>` | `<variables>` | 200 | `{"data":{...}}` | PASS/FAIL |

## Negative Path Evidence
| Scenario | Request | Expected Error | Actual Error | Result |
|---|---|---|---|---|
| Invalid data-source selector | `GET ...?<db-param-name>=invalid_value` | 4xx controlled error | - | PASS/FAIL |
| Missing required GraphQL args | `<queryB>` with missing args | GraphQL validation error | - | PASS/FAIL |

## Open Gaps
- Gap:
- Owner:
- ETA:

## Approval
- QA/Reviewer:
- Decision:
