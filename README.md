# tstack

The agent skills I use every day, cleaned up so anyone can use them. They work
in Claude Code and Codex. Some use [T3 Code](https://github.com/pingdotgg/t3code)
features when they are available and fall back to native tools when they are
not.

## Skills

| Skill | Say | What it does |
|---|---|---|
| `full-send` | "full send" | Takes a change to a merged PR with nobody watching: proves it, reviews it, files it, babysits it, merges it. |
| `full-stop` | "full stop" | Same as `full-send`, but stops when the PR is ready for you to merge. |
| `council` | "council" | Gets three independent OpenAI reviews with different focuses, and loops until they agree. |
| `sol-loop` | "sol loop" | Reviews a change with a selected OpenAI model in a loop until both agents agree. |
| `openai-review-models` | (automatic) | Shared OpenAI-only model selection and delegation policy. |
| `file-pr` | "file a PR" | Files a short PR that leads with the problem, not an inventory of changes. |
| `takeover` | "take over" | Finishes someone else's PR, branch, or agent thread, and keeps their credit. |
| `babysit-pr` | "babysit" | Watches a PR through CI and review bots, and fixes the real findings. |
| `modernize` | "modernize" | Rebases an old PR and decides if it should still exist. |
| `leaving-pr-comments` | (automatic) | Formats comments that an agent posts on your behalf. |

Some skills call others. Install the ones they need too:

- `council` and `sol-loop` use `openai-review-models`.

- `full-send` uses `sol-loop`, `file-pr`, and `babysit-pr`.
- `full-stop` uses `full-send`.
- `takeover` uses `modernize`, `file-pr`, `babysit-pr`, and `leaving-pr-comments`.
- `file-pr`, `babysit-pr`, and `modernize` use `leaving-pr-comments`.

## Install

```bash
git clone https://github.com/timjonaswechler/tstack
cp -R tstack/skills/* ~/.claude/skills/   # Claude Code
cp -R tstack/skills/* ~/.codex/skills/    # Codex
```

## Requirements

- An authenticated [GitHub CLI](https://cli.github.com) (`gh`).
- `python3` for `babysit-pr`'s `pr-wait` script.
- `council` and `sol-loop` need subagents that can run the selected OpenAI
  model. One model and provider instance suffice. See the
  [shared model policy](skills/openai-review-models/SKILL.md).

## Fork updates

This fork keeps its OpenAI-only changes as commits on `main`. `origin` points
to `timjonaswechler/tstack`; `upstream` points to `t3dotgg/tstack`.

With the `~/.agents/justfile` installed:

1. Run `just --justfile ~/.agents/justfile skills install` to clone the fork,
   configure `upstream`, and symlink the skills into `~/.agents/skills`.
2. Commit any local skill edits before updating. Run
   `just --justfile ~/.agents/justfile skills update` to fast-forward from the
   fork and merge `upstream/main`, preserving the fork's own commits. This also
   updates the mattpocock skills. A dirty checkout or a branch other than `main`
   stops the tstack update.
3. If the upstream merge conflicts, resolve the files in `~/.agents/tstack`,
   stage them, and run `git -C ~/.agents/tstack commit`. Alternatively cancel
   that merge with `git -C ~/.agents/tstack merge --abort`. Rerun the update
   after resolving to refresh skill links. A fork fast-forward failure also
   stops the update and requires manual reconciliation; nothing is reset.
4. Check the updated workflows still follow the OpenAI-only policy, then run
   `git -C ~/.agents/tstack push origin main` to publish the merged updates.
   The update command does not push automatically.

`AGENTS_DIR` overrides `~/.agents`; `TSTACK_REPO_URL` overrides the fork URL for
installation.

## Make them yours

These are starting points. Edit them. Put personal settings, like which models
sit on your council, in your `CLAUDE.md` or `AGENTS.md`, so you can update the
skills without losing them.

## License

[MIT](LICENSE)
