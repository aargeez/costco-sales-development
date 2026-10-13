# Agent Contract Template

## Goal
(Single sentence: what outcome this agent is accountable for.)

## Capabilities
- (Skill or tool the agent can use)

## Decision Logic
- (When to proceed, when to escalate, when to refine)

## Memory
- (What context this agent retains across steps: prior outputs, validation results, iteration count)

## Execution Loop
1. **Plan**: Determine approach and acceptance threshold.
2. **Act**: Produce draft artifact using templates and subagent outputs.
3. **Evaluate**: Run validators/checkers against acceptance criteria.
4. **Refine**: Patch artifact based on evaluation findings.
5. **Repeat** until acceptance threshold met or max iterations reached.
6. **Escalate** unresolved items with risk context.

## Exit Criteria
- (Conditions that must be true before this agent marks work complete)

## Artifact Persistence
- (File path pattern where final output is saved, e.g., `docs/stories/<story-id>.md`)
