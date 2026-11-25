---
name: harden-aie
description: "Hunt your OWN Peripety/AIE web properties for defensive hardening — scope-locked to AUTHORIZED-SCOPE.md, web-only vuln classes for the React/Vite/Next/Vercel stack. Output is a fix-it report, never a bug-bounty submission. Usage: /harden-aie [target] [--quick]"
---

# /harden-aie

A defensive wrapper around `/autopilot`, pre-scoped to the assets you own. Use it
to find and fix issues in your **own** sites before anyone else does — not for
bug-bounty submissions. Every request is gated by the deterministic scope engine
against [`AUTHORIZED-SCOPE.md`](../AUTHORIZED-SCOPE.md) (deny wins, default deny).

## Usage

```
/harden-aie                      # hunt every in-scope apex in AUTHORIZED-SCOPE.md
/harden-aie theaie.net           # one property
/harden-aie allthingsai.org --quick   # fast surface pass, lower token use
```

If you name a target, it MUST resolve in-scope or the run is a hard stop. Add new
owned domains to `AUTHORIZED-SCOPE.md` first — do not pass a bare `--in-scope`
override here.

## Scope gate (runs first, every time)

```bash
python3 engine/scope.py <target> --md AUTHORIZED-SCOPE.md
```

`OUT-OF-SCOPE` (non-zero exit) = stop. Do not touch the asset. The out-of-scope
block in `AUTHORIZED-SCOPE.md` deliberately excludes the platforms you rent
(Vercel, Beehiiv, Notion, HubSpot) — test your custom domain, which resolves to
your deployment, never the shared platform host.

## What this does

Runs the standard hunt cycle, but narrowed to the vuln classes that actually
apply to a React/Vite/Tailwind SPA on Vercel with serverless API routes:

```
1. SCOPE     Confirm target against AUTHORIZED-SCOPE.md
2. RECON     Enumerate subdomains, live hosts, and the real attack surface
3. RANK      Prioritize API routes, auth flows, and anything handling user input
4. HUNT      Web classes only (see list below)
5. VALIDATE  7-Question Gate — kill false positives before they reach the report
6. REPORT    Prioritized FIX LIST for you, grouped by severity and file/route
```

### Vuln classes in play (web stack)

Auto-load these hunt skills; skip the enterprise/AD/mobile chains entirely:

- `hunt-xss`, `hunt-dom`, `hunt-html-injection` — reflected/stored/DOM in the SPA
- `hunt-idor`, `hunt-auth-bypass`, `hunt-business-logic` — API route authz
- `hunt-oauth`, `hunt-jwt-crypto`, `hunt-saml`, `hunt-session`, `hunt-mfa-bypass` — identity
- `hunt-ssrf`, `hunt-open-redirect`, `hunt-host-header`, `hunt-cors`, `hunt-csrf` — request handling
- `hunt-nextjs`, `hunt-nodejs`, `hunt-api-misconfig`, `hunt-spa-api`, `hunt-shadow-api` — stack-specific
- `hunt-source-leak`, `hunt-cloud-misconfig`, `hunt-cache-poison`, `hunt-clickjacking` — exposure/config

Out of play for this stack (do not load): `m365-entra-attack`, `okta-attack`,
`vmware-vcenter-attack`, `enterprise-vpn-attack`, `hunt-sharepoint`,
`apk-redteam-pipeline`, `ios-redteam-pipeline`, `hunt-ldap`, `hunt-ntlm-info`.

## Output

A defensive hardening report, not a disclosure draft:

- **Findings** grouped P1→P4, each with the vulnerable route/URL, a minimal
  repro, and a concrete fix (header to set, check to add, config to change).
- **Quick wins** — security headers (CSP, HSTS, X-Frame-Options), exposed
  source maps, verbose error pages, missing rate limits.
- **No submission step.** These are your assets; the deliverable is the fix list.

## Safety

- Read-only / safe methods by default. PUT/DELETE/PATCH require your approval
  even on your own assets (a destructive request against prod is still prod).
- Rate limited and circuit-broken like `/autopilot` (stops on repeated 403/429).
- Nothing is ever auto-submitted anywhere.

## After

- Hand the fix list to `/report` formatting if you want a shareable writeup for
  your team, or feed P1/P2 items straight into the relevant repo
  (`mrhinkle/theaie-net`, `mrhinkle/allthingsai-org`).
- Re-run after you ship fixes to confirm they closed.
