# session-start-action

GitHub Actions workflows for starting Claude Code or Codex sessions on a schedule with a tiny `hi` prompt.

## What this repo does

- `Claude Session Start` sends a short prompt through Claude Code using your Claude OAuth token.
- `Codex Session Start` sends the same kind of prompt through Codex using a base64-encoded `auth.json` secret.
- Both workflows can also be run manually from the GitHub Actions tab.
- Both workflows run every 5 hours by default. GitHub cron schedules use UTC.

## Why this is useful

Claude Code and Codex sessions are easiest to use when they are already warm and running. If your workflow has a 5-hour session window, this repo lets you start that window on a schedule instead of waiting until the moment you sit down to code.

That means you can:

- kick off a session before your work block starts
- keep a session active across the parts of the day when you are away from your desk
- avoid burning time on manual startup when you are ready to work

## Secrets you need

### Claude

Create a repository secret named `CLAUDE_CODE_OAUTH_TOKEN` and paste in your authenticated Claude Code OAuth token.

### Codex

Create a repository secret named `CODEX_CONFIG_B64` and store a base64-encoded copy of your Codex `auth.json` there.

To generate that Codex secret:

1. Find the auth file in your Codex home directory:

   ```bash
   CODEX_AUTH_DIR="${CODEX_HOME:-$HOME/.codex}"
   ls "$CODEX_AUTH_DIR/auth.json"
   ```

2. Base64-encode the file:

   ```bash
   # Linux
   CODEX_AUTH_DIR="${CODEX_HOME:-$HOME/.codex}"
   base64 -w0 "$CODEX_AUTH_DIR/auth.json"

   # macOS
   CODEX_AUTH_DIR="${CODEX_HOME:-$HOME/.codex}"
   base64 -i "$CODEX_AUTH_DIR/auth.json"
   ```

3. Save the encoded output as the `CODEX_CONFIG_B64` secret.

If `CODEX_HOME` is not set, Codex normally keeps `auth.json` under `~/.codex/auth.json`, which is the fallback path used above.

## Workflow files

- `.github/workflows/claude-session-start.yml`
- `.github/workflows/codex-session-start.yml`

## Adjusting the schedule

If you want different run times, edit the `schedule` block in each workflow file. The current cadence is every 5 hours.
