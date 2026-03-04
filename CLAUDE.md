# Gladiator Tournament Scheduler

Web application for scheduling and managing gladiator tournaments.
**Event date: April 25, 2026.**

## Project Structure

```
TournamentScheduler/
├── backend/          # Python (FastAPI + SQLAlchemy)
│   ├── src/tournament/
│   │   ├── api/      # REST endpoints
│   │   ├── models/   # SQLAlchemy models
│   │   ├── scheduling/ # Match scheduling algorithms (Strategy pattern)
│   │   └── core/     # Config, shared utilities
│   └── tests/
├── frontend/         # React + TypeScript (Vite)
│   └── src/
├── .specify/         # Spec-kit artifacts (constitution, specs, plans)
└── .claude/commands/ # Spec-kit slash commands
```

## Development Commands

```bash
# Backend
cd backend && uv sync --all-extras    # Install deps
cd backend && uv run pytest           # Run tests
cd backend && uv run uvicorn tournament.api.main:app --reload  # Dev server (port 8000)

# Frontend
cd frontend && npm install             # Install deps
cd frontend && npm run dev             # Dev server (port 5173, proxies /api to :8000)
cd frontend && npm run build           # Production build
```

## Spec-Kit Workflow

Follow this order for feature development:

1. `/speckit.constitution` - Establish project principles (done once)
2. `/speckit.specify` - Write specification for a feature
3. `/speckit.clarify` (optional) - De-risk ambiguous areas
4. `/speckit.plan` - Create implementation plan
5. `/speckit.checklist` (optional) - Quality validation checklist
6. `/speckit.tasks` - Generate actionable tasks
7. `/speckit.analyze` (optional) - Cross-artifact consistency check
8. `/speckit.implement` - Execute implementation

## Tournament Domain Rules

### Gladiator Classes (6 total)

| Class | Division | Weapon | Shield |
|-------|----------|--------|--------|
| Provocator | Heavy | Gladius (short sword) | Large rectangular |
| Murmillo | Heavy | Gladius (short sword) | Large rectangular |
| Secutor | Heavy | Gladius (short sword) | Large rectangular |
| Thraex | Medium | Sica (curved sword) | Small square |
| Hoplomachus | Medium | Spear + dagger | Small round |
| Retiarius | Light | Trident + net | None |

### Division System

- **Heavy:** Provocator, Murmillo, Secutor
- **Medium:** Thraex, Hoplomachus
- **Light:** Retiarius

### Matchup Rules (HARD CONSTRAINTS)

- **Same-class matchups are FORBIDDEN** (except Provocator vs Provocator)
- Matchups must cross divisions or be within-division cross-class
- Valid examples: Murmillo vs Thraex, Secutor vs Retiarius, Provocator vs Provocator
- Invalid examples: Murmillo vs Murmillo, Thraex vs Thraex

### Scheduling Constraint

These matchup rules break standard elimination brackets. The scheduling algorithm must handle arbitrary class distributions while respecting constraints.

### Algorithm Strategy

- Algorithms are pluggable via Strategy pattern in `backend/src/tournament/scheduling/`
- MVP: simple round-robin within valid matchups
- Advanced algorithms added after research
