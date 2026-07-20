# Comments backend — decision parked for later

**Goal:** private, two-way comments on the will-chem doc pages (diary, hypotheses,
etc.) — comments that come back to Ben, not just personal margin notes, and that
stay private (these pages are unlisted, no password by choice).

**Decision so far:** keep it PRIVATE. Not building it yet; this note captures the
options we compared so we can pick up where we left off.

## Options compared

### 1. Browser-only (localStorage + export) — zero infra
Comments save to the reader's own browser; an "Export" button emits JSON the
reader sends back. This is exactly what the feedback inbox already does.
- Pros: no backend, no accounts, fully static, private.
- Cons: comments never leave that one device unless manually exported/sent.
  Fine for personal notes or a one-person feedback loop; not a shared thread.

### 2. GitHub-backed widget (giscus / utterances) — REJECTED
Stores comments as GitHub Discussions/Issues.
- Rejected because it requires a PUBLIC repo and the comments are publicly
  visible — conflicts with keeping this content semi-private. Also needs each
  commenter to have a GitHub account.

### 3. Cloudflare Worker + KV/D1 — the private, durable pick
A tiny serverless function (no server to run) at e.g.
`will-comments.bchasnov.workers.dev`, backed by Cloudflare KV (simple) or D1
(SQLite). Page JS fetches a page's comments to render them and POSTs new ones.
- Pros: private by default, durable (Ben owns the store), no reader account,
  free tier is plenty, works with unlisted URLs. Section-anchored comments are
  easy since docs are pre-rendered (stable heading IDs).
- Cons: the endpoint is public, so needs a guard — shared secret + rate limit,
  optionally Cloudflare Turnstile. CORS locked to the Pages domain.
- Setup split: Claude writes the Worker + KV schema + frontend JS; Ben runs a
  one-time `wrangler login` && `wrangler deploy` with his own Cloudflare account
  (Claude can't authenticate to Cloudflare from the session).
- **This is the recommended build when we come back to it.**

### 4. Nostr — parked; wrong fit for PRIVATE
Decentralized (keypairs + relays, no server). Interesting, but its defaults are
public + effectively permanent, and readers need a Nostr identity/extension to
post. Making it private means running our own relay with NIP-42 auth — more
exotic infra than the Worker for the same privacy. Only revisit if the goal
flips to a PUBLIC, federated comment layer.

## Next step when we resume
Build Option 3: KV-backed Cloudflare Worker, shared-secret guard, CORS locked to
the Pages domain, section-anchored comment threads on the pre-rendered doc pages.
