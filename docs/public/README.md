# Masker docs (Mintlify)

This is the complete Mintlify project for [docs.masker.dev](https://docs.masker.dev), reflecting the current reality of [github.com/navi25/masker](https://github.com/navi25/masker).

## Local preview

```bash
npm i -g mintlify
cd masker_docs
mintlify dev
```

Mintlify serves a local preview at `http://localhost:3000`.

## Deploy

Mintlify auto-deploys from a connected GitHub repo. To ship these docs:

### Option A — replace files in your existing Mintlify repo

1. Identify the repo that Mintlify is reading from (the one connected at https://dashboard.mintlify.com).
2. Replace the contents with the files in this folder.
3. Commit and push to `main`. Mintlify rebuilds in ~30 seconds.

### Option B — point Mintlify at this folder in `navi25/masker`

If you want the docs to live alongside the code:

1. Move this folder into the main repo at `docs/` (or `mintlify/`).
2. In the Mintlify dashboard, change the connected directory to that path.
3. Push and let it rebuild.

## What was rewritten and why

The previous docs were a stale starter template. This rewrite reflects what's actually in the code today:

- **Two integration endpoints, not an SDK** — `/proxy/{agent_id}/v1/chat/completions` and `/vapi/webhook/{agent_id}`. There's no SDK install step because there's no SDK.
- **GitHub OAuth only** — no email/password, no API keys yet. Long-lived tokens are in the May 30 roadmap and the docs say so.
- **Vault deterministic + reversible AEAD** — both schemes are documented with the actual `MSKV1.*` / `MSK1.*` token formats.
- **9/3/6 Safe Harbor honesty** — the coverage matrix matches the codebase, with explicit "in progress" markers for categories L, M, N, P, R.
- **Real REST routes** — every endpoint matches the `axum` router (`apps/api-server`).
- **Hosted vs. customer VPC vs. air-gapped** — three deployment options, honestly described.
- **Audit log integrity** — HMAC-chained, append-only, recomputable. Documented end-to-end.

## Things to confirm before you ship

A few places where the docs make claims that should be verified against your latest code:

- [ ] Latency numbers (45–95 ms total, 30–80 ms NER) — re-bench on prod hardware
- [ ] `X-Vapi-Signature` header name — confirm exact header in your verification code
- [ ] `agt_*`, `sess_*`, `evt_*` ULID prefixes — match what your code actually emits
- [ ] Stage durations field name (`stage_durations_ms`) — match what your sessions API returns
- [ ] Default policy filename and path (`configs/mask_policy.yaml`) — match your repo

These are standard "docs as a forcing function" items — if the code disagrees, fix one of them.

## Things intentionally left out

- **SDK install commands** — there's no SDK
- **Playground page** — not built yet
- **Multi-user accounts / team invitations** — May 30 roadmap, mentioned in `auth/oauth-callback.mdx`
- **Account-wide and agent-wide compliance report endpoints** — May 30 roadmap, mentioned in `sessions/get-session-report.mdx`

If any of these ship before May 30, add the corresponding pages and update the relevant references.
