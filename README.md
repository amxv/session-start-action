# session-start-action

GitHub Actions workflows for starting Claude Code or Codex sessions on a schedule with a tiny `hi` prompt.

## What this repo does

- `Claude Session Start` sends a short prompt through Claude Code using your Claude OAuth token.
- `Codex Session Start` sends the same kind of prompt through Codex using a base64-encoded `auth.json` secret.
- Both workflows can also be run manually from the GitHub Actions tab.
- Both workflows run every 5 hours by default. GitHub cron schedules use UTC.
- Each workflow is gated by a GitHub repository variable:
  - `CLAUDE_SESSION_START_ENABLED`
  - `CODEX_SESSION_START_ENABLED`

## Enable or disable

Set the matching repository variable to `true` to allow that workflow to run.
Leave it unset or set it to anything else to disable it.

From the repo root, use the GitHub CLI:

```bash
gh variable set CLAUDE_SESSION_START_ENABLED --body true
gh variable set CODEX_SESSION_START_ENABLED --body true
```

To disable a workflow again, delete the matching variable:

```bash
gh variable delete CLAUDE_SESSION_START_ENABLED
gh variable delete CODEX_SESSION_START_ENABLED
```

You can also set the value to something other than `true` if you want to keep the variable around but leave the workflow disabled.

## Why this is useful

Claude Code and Codex sessions are easiest to use when the clock starts before you do.

For example, if your scheduled start times are `10:00 a.m.`, `3:00 p.m.`, and `8:00 p.m.`, then a session that begins at `10:00 a.m.` is already counting down by the time you start coding at `12:00 p.m.`. If you hit the session limit before `3:00 p.m.`, the `3:00 p.m.` start gives you a fresh window right away. Without this repo, you would not reset until `5:00 p.m.` instead.

That is the point: you can move the session window earlier in the day so the reset happens when you need it, not only when you happen to sit down at the keyboard.

## Secrets you need

### Claude

From the repo root, run:

```bash
claude setup-token
```

Copy the token that `claude setup-token` prints, then run:

```bash
gh secret set CLAUDE_CODE_OAUTH_TOKEN
```

Paste the token when `gh` prompts you. That stores it in the current repository.

### Codex

From the repo root, run:

```bash
gh secret set CODEX_CONFIG_B64 --body "$(base64 "${CODEX_HOME:-$HOME/.codex}/auth.json" | tr -d '\n')"
```

If `CODEX_HOME` is not set, the command falls back to `~/.codex/auth.json`.

## Workflow files

- `.github/workflows/claude-session-start.yml`
- `.github/workflows/codex-session-start.yml`

## Adjusting the schedule

If you want different run times, edit the `schedule` block in each workflow file. The current cadence is every 5 hours.
