# Spec: Registration

## Overview
Implement user registration so new visitors can create a Spendly account.
This step wires up the `POST /register` route, validates submitted form data
server-side, persists the new user to the database with a hashed password, and
redirects to the login page on success. It also sets Flask's `SECRET_KEY`
(required for flash messages in later steps). The `GET /register` route and
`register.html` template already exist; only the backend logic needs to be added.

## Depends on
Step 01 — Database Setup (users table, `get_db()`, `init_db()`, `seed_db()` must all be working)

## Routes
- `GET /register` — render registration form — public *(already implemented; no changes needed)*
- `POST /register` — validate form data, insert user, redirect on success — public

## Database changes
No new tables or columns. The existing `users` table has all required fields:
`name`, `email`, `password_hash`, `created_at`.

## Templates
- **Modify:** `templates/register.html`
  - The template already renders `{{ error }}` and has the correct form fields.
  - Repopulate `name` and `email` input values on validation failure so the user
    does not have to retype them (add `value="{{ name or '' }}"` and
    `value="{{ email or '' }}"`).

## Files to change
- `app.py` — add `SECRET_KEY`, update `/register` to accept POST, implement registration logic

## Files to create
None

## New dependencies
No new dependencies. `werkzeug.security` is already installed.

## Rules for implementation
- No SQLAlchemy or ORMs — use `sqlite3` directly via `get_db()`
- Parameterised queries only — never string-format SQL
- Hash passwords with `werkzeug.security.generate_password_hash`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Set `app.secret_key` using a hard-coded dev string for now (e.g. `"spendly-dev-secret"`)
- Validate all three fields server-side before touching the database:
  - `name` must be non-empty
  - `email` must be non-empty (HTML `type="email"` handles format)
  - `password` must be at least 8 characters
- On duplicate email (UNIQUE constraint violation) render the form again with a
  clear error message — do **not** let the `sqlite3.IntegrityError` bubble up
- On success redirect to `GET /login` (the login page); do **not** auto-login the user
  (sessions are a later step)
- Catch `sqlite3.IntegrityError` specifically; let all other DB errors propagate

## Definition of done
- [ ] Submitting the form with valid data creates a new row in `users` with a hashed password
- [ ] Registering with an already-used email shows "An account with that email already exists" and keeps `name` and `email` pre-filled
- [ ] Submitting with any blank field shows a validation error and re-renders the form
- [ ] Password shorter than 8 characters shows a validation error
- [ ] Successful registration redirects to `/login`
- [ ] The `password` field is never echoed back in the rendered HTML
- [ ] App starts without errors (`python app.py`)
