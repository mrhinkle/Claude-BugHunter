# Authorized scope — Mark Hinkle / Peripety Labs owned assets

This file is the default allowlist for hunting your **own** properties. It is
parseable by the deterministic scope engine (`engine/scope.py`), so every
`/harden-aie` run and any `python3 engine/scope.py <asset> --md AUTHORIZED-SCOPE.md`
check is gated against exactly these rules. Deny wins; default deny — anything
not listed here is rejected.

Only add a domain you **own or are explicitly authorized to test**. An apex rule
(`theaie.net`) covers the apex *and* all its subdomains. Do not test shared
platform infrastructure you merely rent (Vercel, Beehiiv, Notion, HubSpot) — hit
your own custom domain, which resolves to your deployment, not the platform.

## In scope

- theaie.net — The AIE Network portal (React/Vite/Tailwind on Vercel)
- allthingsai.org — All Things AI community + conference site
- peripety.com — Peripety Labs parent company
- theaios.ai — AIOS book marketing site (aios-site repo)

## Out of scope

- vercel.app — Vercel platform + preview deployments (test your custom domain, not the host)
- beehiiv.com — newsletter platform (you don't own the infrastructure)
- beehiiv.net — Beehiiv asset/CDN hosts
- notion.so — content calendar / manuscripts platform
- notion.site — published Notion pages
- hubspot.com — CRM platform
- monday.com — project management platform

## Not added (checked)

- The Human Signal (`the-human-signal` repo) is the novel — Markdown + Pandoc
  build for EPUB/PDF, no website. Nothing to harden.
- AI Toolbox (`ai-toolbox` repo) is archived and lives only on a `*.vercel.app`
  preview with no custom domain, so it's out of scope by the rule above. If you
  stand it (or any other property) up on a custom domain you own, add a bullet
  under `## In scope` with the real hostname.

(Kept as prose, not bullets, so the scope engine doesn't read these as parked hosts.)

> Rule reminder: an exact-host-only rule is `re:^host\.example\.com$`. A wildcard
> `*.example.com` matches subdomains but NOT the bare apex. CIDRs like
> `10.0.0.0/8` match any IP in range. See `commands/scope.md`.
