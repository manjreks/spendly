# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Run the app (port 5001, debug mode on)
python app.py

# Run tests
pytest

# Activate the virtual environment (Windows)
venv\Scripts\activate
```

## Architecture

**Spendly** is a Flask 3.x expense tracker with server-side Jinja2 rendering and SQLite storage (in progress).

### Request flow

```
Browser → app.py (Flask routes) → templates/*.html (Jinja2) → static/css|js
```

All routes live in `app.py`. The database layer lives in `database/db.py`, which is currently a stub awaiting implementation. The planned helpers are `get_db()`, `init_db()`, and `seed_db()`.

### Templates

Templates extend `base.html`, which provides the sticky navbar, footer, and links to CSS/JS assets. Page-specific overrides go in the extending template's `{% block content %}`.

### CSS system

`static/css/style.css` defines a CSS variable system in `:root`:
- Colors: `--ink`, `--ink-soft`, `--ink-muted`, `--paper`, `--accent` (green), `--accent-2` (orange), `--danger`
- Fonts: `--font-display` (DM Serif Display), `--font-body` (DM Sans)
- Layout: `--max-width`, `--auth-width`

`static/css/landing.css` contains landing-page-only overrides. All new page styles should use the existing variable system.

Responsive breakpoints: `900px` (single-column, hide hero visual) and `600px` (hide nav links, scale typography).

### Route structure

Implemented: `GET /`, `/register`, `/login`, `/terms`, `/privacy`

Stubbed (marked "Step N" in `app.py`): `/logout`, `/profile`, `POST /expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`

### Brand details

- Icon: `◈` (Unicode, not an image)
- Currency: Indian Rupees (₹)
- Remote: `https://github.com/manjreks/spendly.git`
