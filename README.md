# mac-runner-desktop

Development access to a GitHub-hosted macOS runner over Tailscale.

**Phase 1 (now):** `mac-probe` workflow measures what the runner actually
offers (CPU/RAM/GPU, SIP, hardware video encoder, whether Screen Sharing
shows a real picture) and keeps it reachable on the tailnet for a while.

**Phase 2 (after probe):** a desktop workflow using the best working method
(RustDesk direct-IP over Tailscale, or Apple Screen Sharing/VNC).

## Secrets

| Secret | What |
|---|---|
| `TS_OAUTH_CLIENT_ID` / `TS_OAUTH_SECRET` | Tailscale OAuth client, Auth Keys write, tag `tag:gh-mac` |
| `MAC_PASSWORD` | Password set on the runner's `runner` user (VNC login) |

Tailnet policy: see `tailscale-policy-snippet.hujson`.

## Run

```bash
gh workflow run mac-probe -f runner=macos-15 -f hold_minutes=30
```

Connect: `remmina -c vnc://runner@<tailscale-ip>:5900` (IP is printed in the log).
End early: SSH in and `touch /tmp/stop`, or cancel the run.
