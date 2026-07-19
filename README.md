# lol-cron

This repo is **intentionally public and intentionally empty of source code**.

It exists solely to host a scheduled GitHub Actions workflow that checks out
and runs the actual application code from the private repo
[`chrszhu/lol`](https://github.com/chrszhu/lol) at execution time.

## Why does this repo exist?

GitHub Actions minutes are billed based on the visibility of the repo that
**defines the workflow** — not the visibility of any repo it happens to
check out at runtime. Public repos get unlimited free Actions minutes;
private repos are capped at 2,000 min/month on the Free plan.

By keeping the workflow definition here (public) and the real source code
in `chrszhu/lol` (private), the cron job gets free/unlimited compute without
publishing any actual code. The private repo's contents are pulled onto the
runner's temporary filesystem for the duration of each job and are never
committed or exposed here.

## Required secrets (Settings → Secrets and variables → Actions)

| Secret | Purpose |
|---|---|
| `PRIVATE_REPO_PAT` | Fine-grained PAT scoped **only** to `chrszhu/lol`, with `Contents: Read and write` permission. Read is needed to check out the code; write is needed for the Saturday cache-warm job to commit `static-data/` back. |
| `DATABASE_URL` | Same value as configured in `chrszhu/lol` / Vercel |
| `RIOT_API_KEY` | Same value as configured in `chrszhu/lol` / Vercel |

## Schedule

- `06:00 UTC` daily — refresh NA1 players
- `08:00 UTC` daily — refresh KR players
- Matchup aggregation + CDN cache warm run only on Saturdays

See [`.github/workflows/daily-seed.yml`](.github/workflows/daily-seed.yml)
for the full workflow.
