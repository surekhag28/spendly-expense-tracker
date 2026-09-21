# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Spendly is a Flask-based personal expense tracker (learning project, built incrementally in numbered "steps"). All monetary amounts are in AUD, displayed with an `A$` prefix.

The project is a work in progress: landing, login, register, terms, and privacy pages are live; auth logic, the SQLite database layer, and expense CRUD are still placeholder routes (see `README.md`'s feature status table for what's implemented vs. pending).

## Commands

```bash
# Setup (uv is used in this repo; plain venv/pip also work)
uv venv
source .venv/bin/activate
uv pip install -r requirements.txt

# Run the app (debug mode, http://localhost:5003)
python app.py

# Run tests
pytest

# Run a single test file / test
pytest path/to/test_file.py
pytest path/to/test_file.py::test_name
```

There is no lint/format tooling configured in this repo.

## Architecture

Single-file Flask MVC structure — no blueprints, no app factory:

- **`app.py`** — the entire Flask app. All routes are defined here directly on a module-level `app = Flask(__name__)`. Implemented routes render templates; not-yet-built features (`/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`) currently return placeholder strings and are meant to be filled in incrementally.
- **`templates/`** — Jinja2 templates. `base.html` is the shared layout (navbar, footer, font/CSS/JS includes) with `title`, `head`, `content`, and `scripts` blocks; every page template extends it via `{% extends "base.html" %}` and fills the `content` block. Route links in templates use `url_for('<endpoint>')` (e.g. `url_for('landing')`, `url_for('login')`), matching the Python function names in `app.py`, not the URL paths.
- **`static/`** — `css/style.css` (site-wide styling) and `js/main.js` (client-side behavior, currently minimal).
- **`database/`** — intended to encapsulate all persistence so routes stay free of SQL. `db.py` is a stub to be filled in with:
  - `get_db()` — SQLite connection with `row_factory` and foreign keys enabled
  - `init_db()` — creates tables with `CREATE TABLE IF NOT EXISTS`
  - `seed_db()` — inserts sample dev data
  - The SQLite file (`expense_tracker.db`) is created at the project root at runtime and is git-ignored; each environment gets its own local database.

### Planned request flow (once auth + expenses land)

1. `POST /register` inserts into a `users` table, hashing the password with Werkzeug's `generate_password_hash`.
2. `POST /login` verifies credentials against `users` and starts a session.
3. Authenticated users manage expenses via `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`, each scoped to the logged-in user's rows in an `expenses` table.
4. `/profile` and `/logout` manage the session and account view.

When implementing these, keep SQL access inside `database/db.py` rather than inlining queries in `app.py`.
