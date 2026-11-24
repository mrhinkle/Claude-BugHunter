# Fork notes — Mark Hinkle's personalized Claude-BugHunter

This is a personal fork of [`elementalsouls/Claude-BugHunter`](https://github.com/elementalsouls/Claude-BugHunter)
(MIT / CC-BY-4.0), tuned for hardening my own web properties rather than general
bug-bounty work.

- **Upstream:** `elementalsouls/Claude-BugHunter`
- **Fork point:** upstream `main` @ `9796bee` (2026-11-24)
- **Fork:** `mrhinkle/Claude-BugHunter`

## What this fork adds (the delta from upstream)

- **`AUTHORIZED-SCOPE.md`** — a deterministic, engine-parseable allowlist of the
  assets I own (theaie.net, allthingsai.org, peripety.com), with the platforms I
  only rent (Vercel, Beehiiv, Notion, HubSpot) explicitly denied. Gates every
  scope check: `python3 engine/scope.py <asset> --md AUTHORIZED-SCOPE.md`.
- **`/harden-aie`** (`commands/harden-aie.md`) — a defensive wrapper around
  `/autopilot`, pre-scoped to `AUTHORIZED-SCOPE.md` and limited to the web vuln
  classes that apply to a React/Vite/Next/Vercel stack. Output is a fix-it report,
  never a disclosure submission.

Nothing upstream was deleted or restructured, so syncing stays clean.

## Syncing upstream changes

Upstream is active (368+ commits). To pull updates without losing the two files above:

```bash
git remote add upstream https://github.com/elementalsouls/Claude-BugHunter.git   # one time
git fetch upstream
git merge upstream/main          # our additions are new files — no conflicts expected
git push origin main
```

If a future upstream change ever touches these same files, resolve in favor of
keeping the AIE-specific scope and command.

## Installing this fork

```
/plugin marketplace add mrhinkle/Claude-BugHunter
/plugin install claude-bughunter@elementalsouls
```

Or the copy install (note: `scripts/install.sh` appends a `source` line to your
shell rc and `--all` clones further third-party repos — see `INSTALL.md`):

```bash
git clone https://github.com/mrhinkle/Claude-BugHunter.git
cd Claude-BugHunter && bash scripts/install.sh
```

## Not done yet (deliberately)

Two deeper changes were left for a follow-up where the install smoke tests can be
run to confirm nothing breaks:

- Gating the enterprise/AD/mobile skills (M365, Okta, vCenter, SharePoint, APK,
  iOS) off by default — they don't apply to this stack and add load cost.
- Splitting the ~29.5k-char `skills/bb-methodology/SKILL.md` so the trigger
  payload is lighter.

Both touch how the plugin loads skills, so they shouldn't be done blind.
