<!--
  Sync Impact Report
  - Version change: 0.0.0 → 1.0.0
  - Added principles:
    1. Domain Integrity (new)
    2. Algorithm Pluggability (new)
    3. API-First (new)
    4. Test-Driven (new)
    5. Progressive Deployment (new)
    6. Simplicity First (new)
  - Added sections: Tournament Domain Constraints, Development Workflow
  - Removed sections: none
  - Templates requiring updates:
    - .specify/templates/plan-template.md ✅ compatible (Constitution Check section is generic)
    - .specify/templates/spec-template.md ✅ compatible (user stories + requirements structure fits)
    - .specify/templates/tasks-template.md ✅ compatible (phase-based task structure aligns)
  - Follow-up TODOs: none
-->

# Tournament Scheduler Constitution

## Core Principles

### I. Domain Integrity

Tournament rules (3-division system, 6 gladiator classes, class-matchup restrictions) MUST be encoded as hard constraints in the domain layer. These constraints are non-negotiable and MUST NOT be bypassed by any API endpoint, scheduling algorithm, or UI action.

- Same-class matchups are FORBIDDEN (except Provocator vs Provocator).
- All matchup validation logic MUST live in the backend domain layer, never in the frontend.
- Division and class definitions (Heavy: Provocator/Murmillo/Secutor; Medium: Thraex/Hoplomachus; Light: Retiarius) are fixed and MUST NOT be made user-configurable.

### II. Algorithm Pluggability

Scheduling algorithms MUST be swappable via the Strategy pattern in `backend/src/tournament/scheduling/`. The system MUST NOT be coupled to any single scheduling approach.

- Each algorithm implements a common interface (input: list of fighters with classes; output: list of valid matchups/rounds).
- MVP uses simple round-robin within valid matchups.
- Optimal algorithms (constraint-satisfaction, graph-coloring, etc.) are added after research without modifying consuming code.

### III. API-First

The backend exposes a REST API; the frontend consumes it. All business logic and domain validation MUST reside in the backend.

- The frontend MUST NOT implement domain rules (matchup validation, scoring, eligibility checks).
- API contracts are defined before frontend implementation begins for each feature.
- The frontend proxies `/api` requests to the backend via Vite dev server config.

### IV. Test-Driven

Domain rules (matchup validation, scoring logic, referee eligibility, scheduling constraints) MUST have test coverage. Tests SHOULD be written before or alongside implementation.

- Matchup constraint validation MUST be tested with both valid and invalid class pairings.
- Scheduling algorithm output MUST be tested against domain constraints.
- API endpoints MUST have at least smoke tests for happy-path responses.

### V. Progressive Deployment

Public-facing views (brackets, schedules, results) are read-only. Admin functionality (fighter registration, match management, scoring) starts as local-only.

- Read-only public views are the first deployment target.
- Admin interfaces require no authentication initially (local-only access).
- Authentication is added incrementally when admin access moves beyond localhost.

### VI. Simplicity First

Start with the simplest viable approach. Add complexity only when a concrete need is demonstrated.

- Round-robin league mode first; elimination bracket modes added incrementally.
- SQLite for storage initially; migrate to PostgreSQL only if scaling demands it.
- No premature abstractions: three similar lines of code are better than a speculative helper function.
- YAGNI applies: do not build for hypothetical future requirements.

## Tournament Domain Constraints

These are the fixed domain rules that Principle I (Domain Integrity) protects:

**Gladiator Classes (6):**

| Class | Division | Weapon | Shield |
|-------|----------|--------|--------|
| Provocator | Heavy | Gladius (short sword) | Large rectangular |
| Murmillo | Heavy | Gladius (short sword) | Large rectangular |
| Secutor | Heavy | Gladius (short sword) | Large rectangular |
| Thraex | Medium | Sica (curved sword) | Small square |
| Hoplomachus | Medium | Spear + dagger | Small round |
| Retiarius | Light | Trident + net | None |

**Matchup Matrix:**
- Same-class = FORBIDDEN (except Provocator vs Provocator)
- Cross-division and within-division cross-class matchups = ALLOWED

**Scheduling Impact:**
- Standard elimination brackets break with these constraints because not all fighters can face all others.
- Algorithms MUST handle arbitrary class distributions while respecting the matchup matrix.

## Development Workflow

**Monorepo Structure:**
- `backend/` — Python (FastAPI + SQLAlchemy), managed with `uv`
- `frontend/` — React + TypeScript (Vite)

**Spec-Kit Workflow (for all feature development):**
1. `/speckit.constitution` — Establish/amend project principles (this file)
2. `/speckit.specify` — Write feature specification
3. `/speckit.plan` — Create implementation plan
4. `/speckit.tasks` — Generate actionable tasks
5. `/speckit.implement` — Execute implementation

**Branching:** Feature branches off `master`. Commits reference spec-kit task IDs when applicable.

**Code Quality:**
- Backend: `ruff` for linting/formatting, `pytest` for testing.
- Frontend: ESLint + TypeScript strict mode.

## Governance

This constitution defines the non-negotiable principles for the Tournament Scheduler project. All implementation decisions, code reviews, and architectural choices MUST comply with these principles.

- **Amendments** require updating this file, incrementing the version, and verifying consistency with spec-kit templates.
- **Versioning** follows semantic versioning: MAJOR for principle removals/redefinitions, MINOR for new principles or material expansions, PATCH for clarifications and wording fixes.
- **Compliance** is verified during spec-kit plan phase (Constitution Check gate) and during code review.

**Version**: 1.0.0 | **Ratified**: 2026-03-04 | **Last Amended**: 2026-03-04
