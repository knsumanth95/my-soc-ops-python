# Soc Ops - Agent Customization Guide

> Social Bingo game for in-person mixers. Players find people matching questions and mark 5 in a row.

## ⚠️ MANDATORY Development Checklist

Before committing ANY changes:
- [ ] \python -m uv run ruff check .\ passes (no errors)
- [ ] \python -m uv run pytest\ passes (all 25 tests)
- [ ] Code has type hints on functions/classes
- [ ] No unused imports or variables

## Quick Start

\\\ash
pip install uv
uv sync
python -m uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
python -m uv run pytest                    # Should pass all 25
python -m uv run ruff check .              # Should have no errors
\\\

## Architecture

**Key files**:
- \pp/main.py\ — FastAPI routes + HTMX endpoints
- \pp/game_logic.py\ — Board generation & win detection (well-tested, pure functions)
- \pp/game_service.py\ — \GameSession\ state manager (immutable, frozen models)
- \pp/models.py\ — Pydantic models (\GameState\, \BingoSquareData\)
- \pp/templates/\ — Jinja2 + HTMX components
- \	ests/\ — 25 tests (8 API, 17 logic)

**Directories**:
- \.github/instructions/\ — CSS utilities & frontend design docs
- \workshop/\ — Multi-part learning guide

## How It Works

**Game flow**: START → PLAYING → (5-in-a-row found) → BINGO

**State machine**:
- Managed via \GameSession\ dataclass
- Persisted in signed cookies (\itsdangerous\)
- In-memory: \_sessions\ dict

**HTMX pattern**: User clicks button → FastAPI updates state → Returns partial template → HTMX swaps DOM (no full-page reload)

**Board**: 5×5 grid with center as FREE SPACE (always marked), 24 random questions from \data.py\

## Code Conventions

- **Python**: Type hints required, snake_case for variables/functions, PascalCase for classes
- **Imports**: Absolute (\rom app.*\), organized (stdlib → third-party → local)
- **Models**: All frozen (\ConfigDict(frozen=True)\) for immutability
- **Tests**: Use \TestClient\ fixtures, group in classes, one behavior per test
- **Linting**: Ruff enforces E, F, I, N, W rules from \pyproject.toml\

## Frontend

**Styling**: Use CSS utility classes from \pp.css\ (Tailwind-like). See [CSS Utilities](.github/instructions/css-utilities.instructions.md).

**Templates**: 
- Extend \ase.html\
- Use \{% block content %}\ for page sections
- Use HTMX attributes: \hx-post="/toggle/{{ id }}"\

**Design**: Refer to [Frontend Design](.github/instructions/frontend-design.instructions.md) for distinctive, polished UIs.

## Common Changes

| Task | Where | What |
|------|-------|------|
| Add question | \pp/data.py\ → \QUESTIONS\ | Restart auto-reloads |
| Change board size | \game_logic.py\ + \ingo_board.html\ | Update \check_bingo()\ + tests |
| Add game feature | \game_service.py\ + \models.py\ | Update \GameState\ enum + tests |
| Style component | \pp.css\ | Add utility class or use existing ones |

## Essential Patterns

✅ **DO**: Frozen models, pure functions, type hints, HTMX over JS, test logic changes  
❌ **DON'T**: Modify state outside \GameSession\, relative imports, inline CSS, skip linting

## Commands

\\\ash
# Full validation
python -m uv run ruff check . && python -m uv run pytest

# Specific tests
python -m uv run pytest tests/test_game_logic.py
python -m uv run pytest tests/test_api.py::TestStartGame

# Fix imports/format
python -m uv run ruff check . --fix
\\\

---

**Last Updated**: April 2026 | Copilot Dev Days workshop
