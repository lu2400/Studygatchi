# AGENTS.md

## Cursor Cloud specific instructions

Studygatchi is a Vite/React study-pet UI in `frontend/` and a Django REST API in `backend/`. Install and run commands are in `README.md`.

- Start PostgreSQL with `sudo service postgresql start`. The service does not start on its own in this environment. The image package is PostgreSQL 16 (same major version as CI). Compose in the README uses Postgres 17; 16 is enough for `runserver` and pytest.
- Django loads `backend/.env`, not the repo-root `.env` that Docker Compose uses (`backend/settings/base.py`). For a host `runserver`, set `POSTGRES_HOST=127.0.0.1`. `.env-example` sets `POSTGRES_HOST=postgres`, which only resolves inside Compose. Local role and database: `studygatchi-user` / `studygatchi_db` (password from `.env-example`). Pytest creates `test_studygatchi_db`, so that role needs `CREATEDB`.
- Run Django from `backend/` with the repo-root virtualenv (`.venv`). `uv` is on `PATH` via `/usr/local/bin/uv`.
- Frontend dev server: from `frontend/`, `npm run dev -- --host 0.0.0.0 --port 5173` (http://localhost:5173). ESLint 9 ignores `frontend/.eslintrc.cjs` unless `ESLINT_USE_FLAT_CONFIG=false`. `npm run build` (`tsc -b`) fails on existing TypeScript errors in `GooberMenu.tsx` and `SettingsMenu.tsx`; the Vite dev server still runs.
- CI’s gate is `pytest -m required` from `backend/`. The full suite has an existing failure: `TestTaskIsolation.test_user_cannot_access_task_by_guessing_id` (`GET /api/get_task/` ignores an `id` query and returns 200).
- The React UI does not call the API. Task routes require an authenticated `StudyUser`. `GET /api/ping/` does not.
