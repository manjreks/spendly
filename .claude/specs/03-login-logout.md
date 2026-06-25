# Spec: Login and Logout

## Overview
Wire up the `POST /login` route so registered users can authenticate and
start a Flask session, and implement `GET /logout` to clear that session.
This is the first step that uses `flask.session`, which is already enabled
by the `app.secret_key` set in Step 02. On success, users land on a
placeholder profile page; the full profile is implemented in Step 04.
The navbar in `base.html` should reflect session state — showing "Sign out"
for authenticated users and the existing "Sign in / Get started" for guests.

## Depends on
- Step 01 — Database Setup (`users` table, `get_db()` must be working)
- Step 02 — Registration (users must exist in the database to log in)

## Routes
- `GET /login` — render login form — public *(already implemented; add redirect if already logged in)*
- `POST /login` — validate credentials, set session, redirect to profile — public
- `GET /logout` — clear session, redirect to `/login` — logged-in only

## Database changes
No database changes. The `users` table already has `id`, `email`, and
`password_hash`, which are all that login needs.

## Templates
- **Modify:** `templates/login.html`
  - Repopulate the `email` field on validation failure (`value="{{ email or '' }}"`)
- **Modify:** `templates/base.html`
  - Update `.nav-links` to conditionally show "Sign out" (linking to `/logout`)
    when `session.user_id` is set, or "Sign in" + "Get started" when it is not

## Files to change
- `app.py`
  - Add `session` to the Flask import
  - Add `check_password_hash` to the `werkzeug.security` import
  - Update `GET /login` to redirect to `/profile` if `session.get('user_id')` is already set
  - Update `/login` to accept `POST`; implement credential-check logic
  - Implement `GET /logout`: clear session, redirect to `/login`
- `templates/login.html` — repopulate `email` on error
- `templates/base.html` — conditional nav links based on session state

## Files to create
None

## New dependencies
No new dependencies. `flask.session` and `werkzeug.security.check_password_hash`
are both available from already-installed packages.

## Rules for implementation
- No SQLAlchemy or ORMs — use `sqlite3` directly via `get_db()`
- Parameterised queries only — never string-format SQL
- Verify passwords with `werkzeug.security.check_password_hash`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Session keys to set on login: `user_id` (integer) and `user_name` (string)
- Use `session.clear()` in logout — do not pop keys individually
- On invalid credentials (email not found OR wrong password) show the same
  generic error: "Invalid email or password." — never reveal which was wrong
- Redirect to `/profile` after successful login (profile is a placeholder stub)
- `GET /logout` must redirect to `/login`, not the landing page
- Only `GET /logout` needs a logged-in guard for now; skip `@login_required`
  decorator (that is a later refactor step)

## Definition of done
- [ ] Submitting valid credentials sets `session['user_id']` and redirects to `/profile`
- [ ] Submitting an unknown email shows "Invalid email or password." and keeps `email` pre-filled
- [ ] Submitting the correct email with a wrong password shows "Invalid email or password." and keeps `email` pre-filled
- [ ] Submitting with any blank field shows a validation error without hitting the database
- [ ] Visiting `/logout` clears the session and redirects to `/login`
- [ ] Visiting `/login` while already logged in redirects to `/profile` (no re-login needed)
- [ ] The navbar shows "Sign out" when logged in and "Sign in / Get started" when logged out
- [ ] App starts without errors (`python app.py`)
