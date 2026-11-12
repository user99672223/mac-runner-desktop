# mac-runner-desktop

Remote-control a GitHub-hosted Mac (`macos-15`, Apple M1 virtual, 3 cores,
7 GB RAM, macOS 15) from Linux, over Tailscale, with RustDesk.

## Use

```bash
mac start        # start a Mac (2h default, `mac start 5` for 5h, max 5.8h) and open RustDesk
mac connect      # reopen the RustDesk window
mac ssh          # terminal on the Mac as "dev"
mac status       # is one running? what's its address?
mac stop         # end it
```

**Each session is a brand-new Mac that is wiped when it ends.** Push your
work to git (or copy it off with `scp`) before stopping or before the time runs out.

Don't log in as another user on the Mac's screen (e.g. through Apple Screen
Sharing): it switches the display away from the runner's session and
RustDesk loses the picture until you start a new session.

## How it works

- `.github/workflows/mac-desktop.yml` installs RustDesk, grants it
  screen-capture/input permission (SIP is disabled on these runners), joins
  the tailnet as `gh-mac` with tag `tag:gh-mac`, and starts RustDesk from the
  job step with direct-IP access on port 21118.
- The runner's own desktop sits behind a lock screen from boot (unknown
  password), but RustDesk started from the job inherits the runner agent's
  capture consent and shows and controls the real desktop.
- `.github/workflows/mac-probe.yml` is the diagnostic run (hardware, SIP,
  hardware video encoder, screenshots).

## Setup (done once)

| Secret | What |
|---|---|
| `TS_OAUTH_CLIENT_ID` / `TS_OAUTH_SECRET` | Tailscale OAuth client, Auth Keys write, tag `tag:gh-mac` |
| `MAC_PASSWORD` | RustDesk password and `dev` user password; local copy in `.mac-password` (git-ignored) |

Tailnet policy: `tailscale-policy-snippet.hujson` (only admins can reach
`tag:gh-mac`; the Mac can reach nothing).

GitHub's terms allow Actions only for work on the repository's software
project; use these sessions for development of your projects.
