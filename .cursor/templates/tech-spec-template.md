# Technical Specification Template

## Context
## Scope

## Implementation boundaries
- Target module root (`implementation_module_root`) and package conventions.
- API entry points in scope (`primary_interface`).
- Envelope policy (`wrapper_contract` or `envelope_policy`).
- Optional `reference_locations` used for non-binding style only.

## Functional Requirements
## Non-Functional Requirements

## API design
- Operations, resources, versioning (`contract_version`), pagination/filtering.
- Links to OpenAPI/GraphQL artifacts and endpoint catalog.

## Error, validation, and observability
- Validation approach (bean validation and optional schema validation where required).
- Global exception handling approach; centralized REST advice.
- Reference to `.cursor/templates/backend-rest-orchestration-pattern.md` for controller/status semantics.
- Stable error code matrix location.
- Correlation/request id propagation.

## Service-layer behavior
- Transaction strategy (read-only reads, write transactions).
- Mapping strategy (request/entity/response ownership).
- Persistence exception translation policy:
  - conflict-class integrity exceptions preserved for 409 mapping,
  - generic data-access failures translated to typed persistence exceptions.
- Audit timestamp defaulting strategy when create timestamp is omitted.

## Security and compliance
- Authentication/authorization model.
- CORS/CSRF considerations.
- Data classification and PII handling.

## Data model and migration impact
## Risks and mitigations
## Rollout and rollback
## Open questions
