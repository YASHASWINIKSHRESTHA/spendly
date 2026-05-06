# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Create and activate virtual environment
python -m venv venv
.\venv\Scripts\Activate.ps1          # Windows PowerShell
source venv/bin/activate             # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Run the development server (port 5001)
python app.py

# Run all tests
pytest

# Run a single test file
pytest tests/test_auth.py

# Run a single test by name
pytest tests/test_auth.py::test_login_success
```

## Architecture

This is a Flask + SQLite expense tracking app called **Spendly**. It follows a minimal, framework-light structure — no ORM, no blueprints, no JS framework.

### Request flow

`app.py` is the single entry point. It defines all routes and renders Jinja2 templates directly. The database layer lives in `database/db.py` and is imported by `app.py` as needed.

### Database (`database/db.py`)

Three functions to implement (currently stubbed):
- `get_db()` — returns a SQLite connection with `row_factory = sqlite3.Row` and `PRAGMA foreign_keys = ON`
- `init_db()` — creates all tables using `CREATE TABLE IF NOT EXISTS`
- `seed_db()` — inserts sample data for development

The database file is `expense_tracker.db` at the project root (gitignored).

### Templates

All templates extend `templates/base.html`, which provides the navbar, footer, and loads `static/css/style.css` and `static/js/main.js`.

`templates/landing.html` additionally loads `static/css/landing.css` via `{% block head %}` — this file contains hero-section-only overrides and should not be used for global styles.

### Styling

`static/css/style.css` — global styles, CSS custom properties (variables), all shared components.  
`static/css/landing.css` — hero section overrides, loaded only on the landing page.

CSS variables are defined in `:root` inside `style.css`. Always use these variables (e.g. `var(--accent)`, `var(--ink-muted)`) rather than hardcoded colors.

Fonts: **DM Serif Display** (display headings via `var(--font-display)`) and **DM Sans** (body/UI via `var(--font-body)`), loaded from Google Fonts in `base.html`.

### Implemented routes

| Route | Status |
|---|---|
| `GET /` | Landing page |
| `GET /register` | Register form (no POST yet) |
| `GET /login` | Login form (no POST yet) |
| `GET /terms` | Terms and Conditions page |
| `GET /privacy` | Privacy Policy page |
| `GET /logout` | Stub — Step 3 |
| `GET /profile` | Stub — Step 4 |
| `GET /expenses/add` | Stub — Step 7 |
| `GET /expenses/<id>/edit` | Stub — Step 8 |
| `GET /expenses/<id>/delete` | Stub — Step 9 |

### JavaScript

`static/js/main.js` is intentionally empty — JS is added per-page using `{% block scripts %}` in individual templates. The landing page modal (YouTube embed) is implemented this way in `landing.html`. No JS libraries or frameworks are used.
