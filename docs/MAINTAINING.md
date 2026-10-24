# Maintaining the portfolio

## How it works

| File | Owner | Edit by hand? |
|---|---|---|
| `README.md` | You, except the block between `<!-- portfolio:featured:start -->` and `<!-- portfolio:featured:end -->` | Yes, outside the markers |
| `portfolio.yml` | You | Yes: the editorial source of truth |
| `PORTFOLIO.md` | Generator | **No**: overwritten on every run |
| `scripts/portfolio/` | Code | Only to change behaviour |

The generator reads `portfolio.yml`, lists your public repositories through the GitHub REST API,
and writes `PORTFOLIO.md` plus the featured block in `README.md`. Output contains no timestamps, so
identical inputs give identical files and the workflow commits nothing.

## Adding a project

Do nothing. Within a day of publishing a public repository with a real description (20+ characters,
4+ words, not a placeholder like "test" or "todo"), it appears in `PORTFOLIO.md`, categorised and ranked. To refresh immediately: Actions →
*Refresh portfolio* → *Run workflow*.

To make it better:
- Add GitHub topics (`computer-vision`, `robotics`, …): topics are the most reliable way to categorise.
- Promote it: add an entry under `featured:` in `portfolio.yml`. It then leaves the discovered list.
- Hide it: add its name under `exclude:`, or give it a topic from `discovery.exclude_topics` (e.g. `wip`).
- Fix its category, text or rank: add `overrides: { repo-name: { category: robotics, summary: "…", boost: 1 } }`.
  Overrides always beat automatic classification; `boost` is added to the ranking score.

## Ranking (`scripts/portfolio/select.py::score`)

Weighted sum, tunable under `discovery.weights`: recency (bucketed 90d / 365d), description quality,
topic count, licence present, and a capped, log-scaled star term. Stars are deliberately minor.

## Running locally

```bash
pip install -r requirements-dev.txt
python -m pytest -q
GITHUB_TOKEN=... python -m scripts.portfolio generate -v     # token optional, avoids rate limits
python -m scripts.portfolio generate --check                 # exit 1 if generated files are stale
python -m scripts.portfolio generate --fixture snapshot.json # offline, no network
```

Exit codes: `0` ok, `1` config/marker error or stale (`--check`), `2` GitHub API unreachable or rate
limited, `3` disk write failed (every file is restored). On exit 2 or 3 the portfolio is left exactly as it was.
Supported Python: 3.10+ (CI runs 3.10 and 3.12).

## Failure behaviour

- API errors are retried 3 times with backoff; rate limits stop immediately with a clear message.
- Any failure aborts before writing; existing generated content is never partially overwritten
  (writes are atomic).
- A featured repo that was renamed or made private fails the run with its name in the error.
  Update `featured[].repo` in `portfolio.yml`.
- Release links are looked up for featured repos only, and silently omitted when no release exists.

## Settings you must check once

1. **Actions enabled** for the profile repository.
2. **Branch protection / rulesets on `main`.** The refresh job pushes a commit with the built-in
   `GITHUB_TOKEN`. If `main` requires pull requests, status checks or signed commits, that push is
   rejected and the job fails visibly (nothing is half-applied). Do not weaken protection to fix this.
   Options: leave this small personal profile repo unprotected, or check whether your ruleset can
   exempt the Actions bot; if neither is acceptable the workflow would need to be changed to open a
   pull request instead. Which of these your account supports was not verified here.
3. Workflow permissions: the job requests `contents: write` itself; the repository default may stay read-only.
4. **Scheduled workflows can be paused.** GitHub pauses cron schedules in a repository after a long
   period (about 60 days) with no repository activity, and a refresh that changes nothing makes no
   commit. If discovery seems to stop, open Actions and re-enable the workflow, or run it manually.
5. **Pinned repositories** cannot be set through an API. Pin them in the GitHub UI (Profile → Customize your pins).

## Dependencies and supply chain

`PyYAML` is the only runtime dependency (`>=6,<7`). Actions are pinned to major versions
(`actions/checkout@v6`, `actions/setup-python@v6`) and Dependabot proposes monthly updates for both
Actions and pip. For stricter supply-chain control, pin the actions to full commit SHAs.

## Editorial `evidence` lines

`evidence:` strings in `portfolio.yml` (test counts, CI, releases) are hand-written facts checked on
the date noted in that file. Re-verify them when you change them; the generator does not.
