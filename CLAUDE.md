# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) used as the starter project for the Claude Code course.

## Commands

- `npm install` — install dependencies (Node 22 in CI)
- `npm run dev` — start the API on http://localhost:3000 with auto-reload (`node --watch`)
- `npm test` — run all tests with the built-in Node test runner (`node --test`)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="POST /users"` — run tests whose name matches a pattern
- `npm run lint` — ESLint over the whole repo

CI (`.github/workflows/ci.yml`) runs `npm run lint` then `npm test` on every push and PR. Both must pass.

## Architecture

- `server.js` builds the Express `app`, mounts each router under its resource path (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), so tests import `app` without opening a port.
- `routes/` holds one `express.Router()` file per resource. A new resource means a new file here plus an `app.use()` line in `server.js`.
- `db/store.js` is the only data layer: an in-memory array that resets on restart. Routes call its functions (`getAllUsers`, `getUserById`, `createUser`) and never touch the data directly.
- `tests/` uses `node:test` + `node:assert` with `supertest` against the exported `app`. The store is module-level state shared by every test in a run, so tests must not assume a fixed user count or IDs created by other tests.
- `PORT` comes from the environment only; there is no dotenv, so `.env` is not loaded automatically.

## Conventions

- CommonJS only (`require` / `module.exports`), not ES modules. ESLint is configured with `sourceType: "script"`.
- Use double quotes and semicolons, matching the existing files.
- Errors are JSON of the form `{ "error": "<message>" }` with the matching status (400 for invalid input, 404 for missing). Successful creates return `201`.
- Convert route params with `Number(req.params.id)` before calling the store; store IDs are numbers.
- Write tests with `node:test` and `node:assert`, not Jest or Mocha.
- Leave the app code alone unless asked: this repo is about the Claude setup, not about changing the API.
