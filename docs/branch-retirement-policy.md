# Branch Retirement Policy

Single source of truth for how merged PR branches are retired in this repository.
The automation that enforces this policy lives in
[`.github/workflows/branch-retirement-assistant.yml`](../.github/workflows/branch-retirement-assistant.yml).
Refs [#30](https://github.com/koi2026/mcp-gen-ui-gateway/issues/30).

## Why

After squash-merge, temporary PR branches linger and start to look like product
roadmap branches. The product surface should be readable from the long-lived
branches and the documented Version Ladder, not from a list of stale PR branches.

## Long-lived branches (never auto-retired)

| Pattern | Meaning |
|---------|---------|
| `main` | Public project home and future real-gateway track |
| `pretotype/genui-demo` | Completed Stage 0 regression baseline plus 0.5/0.6 pretotype-family evolution |
| `release/*` | Reserved for documented release branches |
| `support/*` | Reserved for documented maintenance branches |

Every other branch is a temporary PR branch and should be retired after merge.

## Automatic-deletion criteria

The assistant treats a branch as **safe to delete only when all** of these
deterministic checks pass (one-to-one with the workflow's `deterministic_checks`):

| Check | Condition |
|-------|-----------|
| `merged` | The PR was merged (not merely closed). |
| `same_repo_head` | The head branch lives in this repository, not a contributor fork. |
| `branch_exists` | The head ref still exists (not already deleted). |
| `protected_branch` is false | The head ref does not match a long-lived pattern above. |
| `open_pr_same_head` is false | No other open PR uses the same head branch. |
| `open_pr_uses_branch_as_base` is false | No open stacked PR uses this branch as its base. |
| `keep_label` is false | The PR has neither `keep-branch` nor `do-not-delete-branch`. |

When safe, the assistant records this audit line:

> merged into `<base>`, no open stacked PRs, no keep label, branch is temporary

## Human review required (hold)

If any check above fails, the assistant **holds** and leaves a comment instead of
deleting. Maintainers decide. Typical hold cases:

- PR was closed without merge.
- Head branch is on a fork (the bot never touches fork branches).
- The branch is still the base of an open stacked PR.
- The branch is a roadmap, ADR, release, support, or other product-direction branch.
- A `keep-branch` / `do-not-delete-branch` label is present.

## Dry-run is the default

The assistant runs in **comment-only mode** unless deletion is explicitly enabled:

- Default: posts the decision as a PR comment and deletes nothing.
- Enable real deletion via repository variable `BRANCH_RETIREMENT_DELETE_ENABLED=true`,
  or per-run via the `workflow_dispatch` input `delete_enabled=true`.

This lets maintainers watch the bot's decisions accumulate before granting it
deletion rights. The decision kind is one of `delete`, `comment-only`, or `hold`.

## AI-assisted wording, deterministic decisions

The pass/fail decision is computed entirely from the deterministic checks above.
An optional OpenRouter call (enabled only when `OPENROUTER_API_KEY` is set;
model from `BRANCH_RETIREMENT_MODEL`, default `google/gemini-3.1-flash-lite`) may rephrase the
maintainer comment for readability. **The AI never overrides a safety gate** —
if the call is unavailable, a deterministic comment is used instead.

## Opt out

Add `keep-branch` or `do-not-delete-branch` to any PR whose head branch must
survive merge (stacked intermediates, demo references, release candidates).
These labels must exist in the repository for the opt-out to take effect.

## Manual branch hygiene

When retiring a branch by hand after squash-merge:

```bash
git push origin --delete <branch>   # remote
git fetch --prune && git branch -d <branch>   # local
```

The only exceptions are the long-lived branches listed above.
