# ADR-NNNN — [Title]

**Status**: Proposed | Accepted | Deprecated | Superseded by ADR-XXXX
**Date**: YYYY-MM-DD
**Implemented in**: PR #XX (commit hash optional)
**Related**: ticket IDs, related ADRs, related specs in `specs/features/`

## Context

What was the situation before this decision? What problem are we solving? What constraints are at play (technical, business, organizational)? Cite evidence: data, incidents, audits, user feedback.

Be honest about prior state — including the not-pretty parts. Future maintainers need to understand WHY the codebase looks the way it does.

## Decision

What did we decide? Be specific:

- What's the new approach
- What APIs / abstractions / patterns it introduces
- What it replaces (if anything)
- Where in the code the implementation lives

If there are sub-decisions inside the main decision, list them.

## Consequences

### Positive
- Benefits: solved problems, new capabilities, removed tech debt

### Cost / migration
- What needs to change in the codebase
- What needs to change in operations (deploys, runbooks, monitoring)
- What needs to change for users (UI, API contracts, behavior)
- Estimated effort, risks, blockers

### Tests
- New tests written
- Existing tests modified
- Coverage gaps acknowledged

### Tech debt created
- TODOs left behind, with conditions for removal
- Compromises accepted, with reason

## Alternatives considered

For each alternative seriously considered:
1. **Alternative A**: brief description. Why rejected.
2. **Alternative B**: brief description. Why rejected.

Documenting alternatives is critical — future maintainers may want to revisit and need to understand what was already explored.

## Open questions

Anything decided "for now" that may need revisiting. Conditions that would trigger a revisit.
