# session-start-action

GitHub Actions workflows for starting Claude Code or Codex sessions on a schedule with a tiny `hi` prompt.

## What this repo does

- `Claude Session Start` sends a short prompt through Claude Code using your Claude OAuth token.
- `Codex Session Start` sends the same kind of prompt through Codex using an OAuth-backed `auth.json` secret.
- Both workflows can also be run manually from the GitHub Actions tab.
- Both workflows run every 5 hours by default. GitHub cron schedules use UTC.

## Secrets you need

### Claude

Create a repository secret named `CLAUDE_CODE_OAUTH_TOKEN` and paste in your authenticated Claude Code OAuth token.

### Codex

Create a repository secret named `CODEX_CONFIG_B64` and store a base64-encoded Codex `auth.json` file there.

To generate that Codex secret:

1. Pull the Codex Docker image used by the reference action:

   ```bash
   docker pull ghcr.io/icoretech/codex-docker:0.142.0
   ```

2. Run the device-auth flow and let it write credentials into a local `.codex/` directory:

   ```bash
   mkdir -p .codex
   docker run --rm -it \
     -v "$PWD/.codex:/home/codex/.codex" \
     ghcr.io/icoretech/codex-docker:0.142.0 \
     codex-bootstrap device-auth
   ```

3. Confirm that `.codex/auth.json` exists.

4. Base64-encode the file:

   ```bash
   # Linux
   base64 -w0 .codex/auth.json

   # macOS
   base64 -i .codex/auth.json
   ```

5. Save the encoded output as the `CODEX_CONFIG_B64` secret.

## Workflow files

- `.github/workflows/claude-session-start.yml`
- `.github/workflows/codex-session-start.yml`

## Adjusting the schedule

If you want different run times, edit the `schedule` block in each workflow file. The current cadence is every 5 hours.
