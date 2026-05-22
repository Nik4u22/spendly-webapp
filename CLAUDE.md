# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project context

This is **Spendly**, a Flask-based personal expense tracker. The codebase is structured as an **incremental, step-by-step learning project** — `app.py` currently has placeholder routes referencing future steps (e.g. `"Logout — coming in Step 3"`, `"Add expense — coming in Step 7"`), and `database/db.py` is an empty skeleton with comments describing functions the student is expected to write (`get_db`, `init_db`, `seed_db`).

When asked to "add a feature," assume the student is working through that curriculum: implement the smallest thing that fulfills the step rather than building out an entire feature set. Don't pre-build later steps (e.g., don't add expense CRUD when working on auth).

## Commands

All commands assume PowerShell on Windows and a venv at `./venv` (gitignored).

```powershell
# First-time setup
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

# Run the dev server (debug mode, port 5001 — NOT the default 5000)
python app.py

# Tests (pytest + pytest-flask are installed but no tests/ directory exists yet)
pytest
pytest path/to/test_file.py::test_name   # single test
```

The SQLite database file `expense_tracker.db` is gitignored and gets created on first run once `init_db()` is implemented.

## Architecture

Classic Flask single-file app:

- [app.py](app.py) — all routes live here. Currently most routes just `render_template` or return placeholder strings. The `if __name__ == "__main__"` block runs on **port 5001**, not 5000.
- [database/db.py](database/db.py) — intended home for `get_db()` (SQLite connection with `row_factory` and `PRAGMA foreign_keys = ON`), `init_db()` (CREATE TABLE IF NOT EXISTS), and `seed_db()`. Empty until the student writes it.
- [templates/](templates/) — Jinja2 templates. All pages extend [base.html](templates/base.html), which provides the nav (Sign in / Get started links), `{% block content %}`, footer, and pulls in `style.css` + `main.js`. Use `url_for('route_name')` for links, not hardcoded paths.
- [static/css/style.css](static/css/style.css) — the design system. CSS custom properties at the top of the file (`--ink`, `--paper`, `--accent`, `--font-display`, `--radius-md`, etc.) define the visual language. **Use these variables** rather than hardcoding colors/spacing when adding styles, and keep the "Spendly" brand voice (DM Serif Display for headings, DM Sans for body, paper-and-ink palette).
- [static/js/main.js](static/js/main.js) — empty stub; students add JS as features need it.

## Conventions to preserve

- Currency in the UI is **₹ (rupees)** — see mock data in [landing.html](templates/landing.html). Don't switch to `$` when adding examples.
- Template forms `POST` to the same route they render from (see [login.html](templates/login.html), [register.html](templates/register.html)) and conditionally render an `{% if error %}` block — match this pattern when adding new forms.
- Route placeholders use the literal string format `"<Feature> — coming in Step N"`. When implementing one, replace the placeholder rather than adding a parallel route.
