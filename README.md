# eqrm/.github

Org-level GitHub configuration and automation.

## Project sweeper

`.github/workflows/project-sweeper.yml` keeps the **IT-Team** project (`#5`) fed with new
issues from every non-archived repo in the org.

### Why it exists

The built-in "Auto-add to project" workflow targets **one repository per workflow**, and the
number of project workflows is capped by plan:

| Plan | Max project workflows |
|---|---|
| Free | 1 |
| Pro | 5 |
| Team | 5 |
| Enterprise Cloud | 20 |
| Enterprise Server | 20 |

`eqrm` is on **Team**, so all five slots are spent (churchtools-connector, ct-cli,
ct-extensions, eq-rpi-printer, non-repo-issues). There is no org-wide auto-add filter on any
plan, and Enterprise Cloud's 20 would still not cover ~33 repos.

Those five built-in workflows **stay enabled** — they give instant add on the busiest repos.
The sweeper is the net underneath them for everything else, on a 30-minute cron.

### Required app setup

The sweeper uses the org `APP_ID` / `APP_PRIVATE_KEY` secrets. That app needs two changes it
did **not** have when the sweeper was written:

1. **Organization permission `Projects: Read and write`.** Without it, `gh project item-add`
   fails — the repo-scoped `GITHUB_TOKEN` cannot write to org projects at all.
2. **Installed on _All repositories_**, not a selected subset, so the sweeper can list issues
   in repos it does not otherwise touch. New repos are then covered with no further action.

Both are changed in the app's settings; existing installations must approve the added
permission before it takes effect.

### Operating it

- Manual backfill after an outage: run the workflow with a larger `lookback_hours`.
- Adding an item already on the board is a no-op, so re-running is always safe.
- Pull requests are swept only from the repos in `PR_REPOS`, to keep Renovate PRs from ~33
  repos off the board while preserving the PR coverage the project has today.
