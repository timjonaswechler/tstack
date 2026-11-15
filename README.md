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
| `council` | "council" | Gets three OpenAI reviews with different focuses; read-only on request, otherwise at most three rounds by default. |
| `sol-loop` | "sol loop" | Reviews a change with a selected OpenAI model within a bounded budget; unresolved findings are not approval. |
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

Our workspace uses [Fleet](https://github.com/timjonaswechler/fleet), not the
former agents justfile. The checkout lives at `repositories/t3dotgg/tstack`;
Fleet links the selected skills into the agents repository and updates only
reviewed release revisions. Matt Pocock's collection is no longer an active
dependency or an automatically updated repository.

Keep local edits intact and follow Fleet's prepare → review → release flow.
Dirty checkouts and divergent history block updates rather than being reset.
A server preparation does not publish a release; client updates only receive
approved revisions. Follow the Fleet documentation for installation and rollout.

Standalone use still works through the installation commands above. Own
verification/diagnosis skills can supply current-state evidence to these flows;
they are not prerequisites, do not add permissions, and do not replace review
or merge gates.

## Make them yours

These are starting points. Edit them. Put personal settings, like which models
sit on your council, in your `CLAUDE.md` or `AGENTS.md`, so you can update the
skills without losing them.

## License

[MIT](LICENSE)
