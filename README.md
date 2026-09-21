# Spendly

> Track every dollar. Own your finances.

Spendly is a Flask-based personal expense tracker. It lets users create an account, sign in, and (as features are completed) log, edit, and delete their expenses. The project is currently a work in progress — auth pages and layout are built, while the database layer and expense CRUD features are being implemented incrementally.

All monetary amounts in the app are denominated in **Australian Dollars (AUD)**, displayed with the `A$` prefix.

## Status

🚧 **In development.** Landing, login, and register pages are live. Authentication logic, the SQLite database layer, and expense management (add/edit/delete) are placeholder routes, being built step by step.

| Feature | Status |
|---|---|
| Landing page | ✅ Done |
| Register page (UI) | ✅ Done |
| Login page (UI) | ✅ Done |
| Database layer (`get_db`, `init_db`, `seed_db`) | 🚧 Not started |
| Register / Login logic | 🚧 Not started |
| Logout | 🚧 Not started |
| Profile page | 🚧 Not started |
| Add / edit / delete expenses | 🚧 Not started |

## Architecture & Workflow

Spendly follows a simple, Flask-native MVC-style structure:

```
Browser
  │
  ▼
app.py  ──────────────►  routes handle requests (/, /login, /register, ...)
  │                          │
  │                          ▼
  │                    templates/*.html  (Jinja2 views, extend base.html)
  │                          │
  │                          ▼
  │                    static/css, static/js  (styling & client-side behavior)
  │
  ▼
database/db.py  ──────►  SQLite (expense_tracker.db)
                          get_db() / init_db() / seed_db()
```

- **`app.py`** is the single Flask application and defines all routes. It renders Jinja2 templates for pages that exist today (`landing`, `login`, `register`) and returns placeholder text for features not yet implemented (`logout`, `profile`, expense add/edit/delete).
- **`templates/`** holds Jinja2 templates. `base.html` is the shared layout (navbar, footer, CSS/JS includes); page templates (`landing.html`, `login.html`, `register.html`) extend it via `{% extends "base.html" %}` and fill in the `content` block.
- **`static/`** holds front-end assets — `css/style.css` for styling and `js/main.js` for client-side behavior (currently minimal, to be expanded as features are built).
- **`database/`** encapsulates all persistence logic. `db.py` will expose:
  - `get_db()` — returns a SQLite connection with `row_factory` and foreign keys enabled
  - `init_db()` — creates tables using `CREATE TABLE IF NOT EXISTS`
  - `seed_db()` — inserts sample data for local development

  This keeps SQL/data access out of `app.py`, so routes stay focused on request handling.
- The SQLite database file (`expense_tracker.db`) is created at the project root at runtime and is git-ignored — each developer/environment gets their own local database.

### Planned request flow (once auth + expenses are implemented)

1. User registers via `POST /register` → a new row is inserted into a `users` table, password hashed via Werkzeug's `generate_password_hash`.
2. User logs in via `POST /login` → credentials verified against the `users` table; a session is started on success.
3. Authenticated user manages expenses via `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete` → each reads/writes an `expenses` table scoped to the logged-in user.
4. `/profile` and `/logout` manage the session and account view.

## Project Structure

```
expense-tracker/
├── app.py                  # Flask app entry point — all routes
├── requirements.txt        # Python dependencies
├── .gitignore               # excludes venv/, *.db, __pycache__/, .env, etc.
│
├── database/
│   ├── __init__.py         # makes `database` an importable package
│   └── db.py                # SQLite connection, schema (init_db), seed data (seed_db)
│
├── templates/                # Jinja2 HTML templates
│   ├── base.html             # shared layout: navbar, footer, CSS/JS includes
│   ├── landing.html          # "/" homepage
│   ├── login.html            # "/login" — sign-in form
│   └── register.html         # "/register" — account creation form
│
└── static/
    ├── css/style.css         # site-wide styling
    └── js/main.js            # client-side JavaScript
```

## Tech Stack

- **Backend:** [Flask](https://flask.palletsprojects.com/) 3.1
- **Database:** SQLite (via Python's built-in `sqlite3`)
- **Templating:** Jinja2 (bundled with Flask)
- **Testing:** [pytest](https://docs.pytest.org/) + [pytest-flask](https://pytest-flask.readthedocs.io/)
- **Frontend:** Vanilla HTML/CSS/JS

## Getting Started

### Prerequisites

- Python 3.13 (or a compatible 3.x version)
- [uv](https://github.com/astral-sh/uv) (used below) — or `pip`/`venv` if you prefer

### 1. Clone the repository

```bash
git clone git@github.com:surekhag28/spendly-expense-tracker.git
cd spendly-expense-tracker
```

### 2. Create and activate a virtual environment

```bash
uv venv
source .venv/bin/activate
```

<details>
<summary>Using plain <code>venv</code> instead</summary>

```bash
python3 -m venv .venv
source .venv/bin/activate
```
</details>

### 3. Install dependencies

```bash
uv pip install -r requirements.txt
```

<details>
<summary>Using plain <code>pip</code> instead</summary>

```bash
pip install -r requirements.txt
```
</details>

### 4. Run the app

```bash
python app.py
```

The app starts in debug mode on **http://localhost:5003**.

> If port `5003` is already in use, stop the process using it or change the `port` value in `app.run(...)` at the bottom of `app.py`.

### 5. Run tests

```bash
pytest
```

## Environment Variables

None are required yet. A `.env` file is already git-ignored for when secrets (e.g. `SECRET_KEY` for sessions) are introduced.

## License

Not yet specified.
