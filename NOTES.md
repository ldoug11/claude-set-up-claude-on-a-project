# Notes

## What's in CLAUDE.md, and what I left out

I kept four parts: a one-line description, the commands, the architecture and the conventions. For commands I included how to run a single test file or tests by name (`node --test --test-name-pattern=...`), because that isn't in `package.json` and Claude would otherwise run the whole suite every time. The architecture section covers the things you only learn by reading several files together: `server.js` exports `app` and only listens when run directly, so tests can import it; every route goes through `db/store.js`; the in-memory store is shared by all tests in a run; and `.env` isn't loaded because there's no dotenv. The conventions are rules Claude can follow without asking: CommonJS not ES modules, the `{ "error": ... }` response shape with 400/404/201, `Number()` on route IDs, `node:test` not Jest, and leave the app code alone.

I left out a file-by-file listing (Claude can see the tree), generic advice like "write good tests", the course instructions from the README, and anything from `.env`. Every line is either something Claude would get wrong without it or something that saves it reading several files.

## Permission rules

- **Allow:** `npm test`, `node --test` (for single tests) and `npm run lint`. They're read-only checks I want Claude to run constantly without a prompt each time.
- **Ask:** `git push`, so nothing leaves my machine without me seeing it first.
- **Deny:** reading `.env` (via the Read tool and `cat .env`), and `git push --force` / `-f`.

Without the `.env` deny rule, Claude could read real secrets into the conversation. It could also copy them into a file, a commit or a PR description. This repo showed why that matters: the original `.gitignore` had leading spaces on every line, so `.env` wasn't actually ignored and `git add .` would have committed it. I fixed the `.gitignore`, but the deny rule is a second layer that doesn't depend on it. The deny rule doesn't catch every shell trick for reading a file, so it's a guard rail, not a guarantee. The force-push deny stops Claude from rewriting shared history on the remote.

## Verification

- `/memory`: TODO, confirm CLAUDE.md shows as loaded in a fresh session.
- `/permissions`: TODO, confirm the allow / ask / deny rules above are listed.
- "How do I run the tests here?": TODO, confirm Claude answers from CLAUDE.md.
