# Spec: Registration

## Overview
This step wires up the registration form so new users can create a Spendly account. The `POST /register` route validates the submitted data, hashes the password with werkzeug, inserts a row into the `users` table, and redirects to the login page on success. On failure it re-renders the form with an error message and the user's previously entered values (sticky inputs). This is the first step that writes user-generated data to the database and establishes the pattern all future write-routes will follow.

## Depends on
- Step 1 — Database Setup: `users` table must exist and `get_db()` must return a working connection.

## Routes
- `GET /register` — render empty registration form — public *(already implemented)*
- `POST /register` — validate form data, insert user, redirect to `/login` on success, re-render with error on failure — public

## Database changes
No database changes. The `users` table (with `id`, `name`, `email`, `password_hash`, `created_at`) was created in Step 1.

## Templates
- **Create:** None
- **Modify:**
  - `templates/register.html` — add `value` attributes to `name` and `email` inputs so the form is sticky on validation errors (values are preserved when the form is re-rendered with an error)

## Files to change
- `app.py` — update `register()` to handle both GET and POST; import `request`, `redirect`, `url_for` from `flask`; add `app.secret_key`; import `generate_password_hash` from `werkzeug.security`; import `get_db` (already imported)
- `templates/register.html` — sticky `value` attributes on `name` and `email` inputs

## Files to create
None

## New dependencies
No new dependencies. `werkzeug.security` ships with Flask and is already used in `database/db.py`.

## Rules for implementation
- No SQLAlchemy or ORMs — use raw `sqlite3` via `get_db()`
- Parameterised queries only — never use string formatting in SQL
- Hash passwords with `werkzeug.security.generate_password_hash` before storing
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Password must be at least 8 characters — validate server-side before attempting the INSERT
- Catch `sqlite3.IntegrityError` to detect duplicate email; surface as a user-facing error message
- Pass `name` and `email` back to the template on error so inputs are sticky
- On success, redirect to `/login` using `redirect(url_for('login'))` — do not render a template
- `app.secret_key` must be set (required by Flask internals, and needed by Step 3)
- Do not use `flask.flash` — pass `error` as a template variable (the template already supports `{% if error %}`)

## Definition of done
- [ ] `GET /register` still renders the empty form without errors
- [ ] Submitting the form with valid data creates a new row in the `users` table (verifiable via sqlite3 CLI or a test)
- [ ] After successful registration the browser lands on `/login`
- [ ] Submitting with an email that already exists shows an error message on the form
- [ ] Submitting with a password shorter than 8 characters shows an error message on the form
- [ ] On a validation error, the `name` and `email` fields retain the submitted values
- [ ] The stored `password_hash` is not the plain-text password (verifiable by inspecting the DB)
- [ ] No SQL string formatting — all queries use `?` placeholders
