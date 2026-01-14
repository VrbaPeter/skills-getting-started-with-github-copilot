<!-- Brief, actionable guidance for AI coding agents working on this repository -->
# Repository overview

- **What this project is:** a tiny FastAPI app that serves a static frontend (SPA) and exposes two simple endpoints to view and sign up for extracurricular activities.
- **Key files:** `src/app.py` (API + static mount), `src/static/index.html` + `src/static/app.js` + `src/static/styles.css` (frontend), `requirements.txt` (dependencies).

# Big picture & architecture

- The server is a single-process FastAPI application that mounts the `src/static` folder at `/static` and exposes JSON endpoints under the root:
  - `GET /activities` — returns the in-memory `activities` dictionary (see `src/app.py`).
  - `POST /activities/{activity_name}/signup?email=...` — appends the `email` to the activity's `participants` list (no persistence).
- Frontend is a static JS app (`src/static/app.js`) which calls these endpoints relatively (e.g. `fetch('/activities')`). The app expects exact activity names as keys.
- There is no external DB or auth: state is kept in-process in `src/app.py`'s `activities` object. Restarting the process resets data.

# Developer workflows (commands)

- Install dependencies (recommended in a virtualenv):

  ```bash
  python3 -m pip install -r requirements.txt
  ```

- Run locally with auto-reload for development:

  ```bash
  uvicorn src.app:app --reload --host 0.0.0.0 --port 8000
  ```

- API quick examples:

  - List activities:

    ```bash
    curl http://localhost:8000/activities
    ```

  - Sign up a student (example):

    ```bash
    curl -X POST "http://localhost:8000/activities/Chess%20Club/signup?email=student@school.edu"
    ```

- Tests: none are provided. `pytest` will use `pytest.ini` (pythonpath = .) if you add tests.

# Project-specific patterns & gotchas

- In-memory data: modify `activities` in `src/app.py` to add/remove sample data; changes are immediate but ephemeral (no persistence).
- Activity names are used as path segments — they must match exactly. The frontend populates the `<select>` from `GET /activities` and uses that value.
- Route functions are written synchronously (normal def) though FastAPI accepts `async def` as well — preserve existing style when adding endpoints unless you intentionally switch.
- Static files live under `src/static` and are mounted at `/static` (so `index.html` is served at `/static/index.html`). The root `/` redirects to that page.

# Integration & external dependencies

- Minimal dependencies: `fastapi`, `uvicorn` (see `requirements.txt`). No database, third-party APIs, or cloud services are referenced.

# How AI agents should make changes

- Short, focused PRs: prefer minimal diffs (e.g., add a new route or update `activities` fixture). Show example curl commands or a short manual test for any runtime change.
- For any endpoint changes, update `src/static/app.js` if the frontend behavior must change (it calls `/activities` and `/activities/{name}/signup`).
- If adding persistence, indicate where to wire it in `src/app.py` (replace in-memory `activities` reads/writes) and include migration notes.

# Files to inspect for common tasks

- Add an activity or change seed data: `src/app.py` (top-level `activities` dict).
- Change UI text or behavior: `src/static/index.html` and `src/static/app.js`.
- Run or debug locally: `requirements.txt` and the `uvicorn` command above.

# When in doubt

- Run the dev server with `--reload` and exercise the app in the browser at `http://localhost:8000/static/index.html` or use the curl examples above.
- Ask for clarification if you need persistent storage, authentication, or new API contracts — current design intentionally keeps the stack minimal for teaching/demo purposes.

---
Please review these notes — tell me if you'd like more examples (unit tests, CI snippets, or a small persistence example).
