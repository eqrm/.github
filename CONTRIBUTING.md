# Contributing

These are the defaults for every repository in this organisation. A repository that needs something
different keeps its own `CONTRIBUTING.md`, which overrides this file entirely.

## Language

English for issues, pull requests and commit messages — one language across the tracker keeps search,
deduplication and cross-references working. User-facing text (UI copy, end-user handbooks) is written
in the language of its audience, which for most of our projects is German.

## Issues: triage → milestone → done

Three levels, each with exactly one job. Confusing them is what lets a tracker drift into hundreds of
open issues that nobody can rank.

| Level | Means | Lifetime |
|---|---|---|
| `area:*` label | the product line it belongs to | permanent, never "done" |
| **Milestone** | a shipping increment we are committed to — the WIP unit | closes when it ships; keep 3–5 open |
| Epic (sub-issue parent) | the spec and its decomposition | reference material; never scheduled |

**An issue is active if and only if it has a milestone.** That one rule replaces every "is this still
relevant?" conversation.

- **`triage`** means unsorted intake and nothing else — nobody has looked at it yet.
- **Sweep it weekly.** Each issue goes one of three ways: give it a milestone (scheduled), remove
  `triage` and leave it unmilestoned (a kept idea), or close it. **Removing the label is the act of
  deciding** — that is the only thing the sweep owes.
- **Nothing is closed for being old.** An unmilestoned issue is a searchable idea, not a failure.

### Sub-issues carry their own context

An epic's sub-issue is usually read alone, by someone who never sees the epic. A sub-issue saying
"apply the mapping from the table above" cannot be implemented from itself — the concrete values
belong in the sub-issue body, even where that duplicates the epic. The epic stays the spec; the
sub-issue stays the work order.

## Branching: trunk-based

`main` is the trunk and the only integration branch. There is no long-lived development branch.

- Branch off `main`, named for what the change is: `feat/*`, `fix/*`, `docs/*`, `chore/*`.
- Open a pull request back into `main`.
- Pull requests **must be squash-merged**: the branch collapses into one commit. The merge-commit and
  rebase buttons are often still enabled — do not use them. They put the branch's raw commits on the
  trunk, which breaks both the version bumps below and the check on the next line.

Because merges are squashes, `git branch --merged` does not detect landed branches — check the pull
request state instead.

Unfinished or risky work ships to `main` behind a feature flag rather than waiting on a long-lived
branch. A branch that lives for weeks is a merge conflict with a delivery date.

## Conventional commits

**The pull request title must be a [conventional commit](https://www.conventionalcommits.org/)** —
and so must every commit on the branch. Both, because which of the two becomes the squash commit
message is a per-repository setting: under GitHub's default, a branch with a single commit lands
under *that commit's* subject and the pull request title is discarded entirely. Writing both
correctly is the only way to be right either way.

Release tooling reads the resulting message to pick the version bump:

| Prefix | Bump |
|---|---|
| `fix:` | patch |
| `feat:` | minor |
| `feat!:` / `fix!:`, or a `BREAKING CHANGE:` footer in a **commit message** | major |
| `chore:`, `docs:`, `refactor:`, `test:`, `ci:` | none |

The squash commit body is assembled from the branch's commit messages, not from the pull request
description. A `BREAKING CHANGE:` footer written only in the description never reaches the commit and
so never produces a major bump — put it in a commit message, or use the `!` marker in the title,
which survives either setting.

A bug fix ships with a **regression test** that fails before the fix and passes after. A green suite
is not proof the bug is covered.

## Merging

The squash-merge is the last irreversible step — the commit is on the trunk immediately, and in
repositories that deploy from it, so is the deploy. Three checks:

- **Run the formatter and the test suite for real** and read the output. "Looks clean" reaching CI as
  a failure is the most common way a merge gets reverted.
- **Match the checks to the head commit**, not the workflow name. An older green run of the same
  workflow is the trap: it looks like the answer and describes a different commit.
- **Where the trunk auto-deploys, let one deploy finish before merging the next pull request.**
  Deploys that overlap can leave an environment wedged, and the second merge is what causes it.

Then squash-merge, delete the branch, and confirm the deploy it triggered actually ran. A merge is
not a deploy.

## Stacked pull requests

For a change that is genuinely several changes — an epic split into steps, or a batch of independent
fixes — prefer a [stack](https://docs.github.com/en/pull-requests/get-started/about-stacked-prs) over
one large pull request. Each layer is reviewed on its own, and each lands as its own conventional
commit, so a batch of twenty fixes produces twenty changelog entries instead of one.

Use a stack when the layers are separately reviewable. A single cohesive change stays a single pull
request — most are, and stacking those adds ceremony without adding review signal.

Two things to know before merging one:

- Merging the top of a stack merges every layer beneath it at once. Where the trunk auto-deploys,
  that is several deploys back to back — the staggering rule above still applies, so merge layer by
  layer in that case.
- Auto-merge does not work with stacks, and the branches must all live in this repository —
  cross-fork stacks are not supported.

## Security

Please do not report security problems in a public issue. See [SECURITY.md](SECURITY.md).
