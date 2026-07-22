# Projects

Unlisted project pages. Nothing here is linked from the homepage — share URLs directly.

## Conventions

- Each project lives under `projects/<project-name>/`.
- Each page within a project gets its own directory, with **dated snapshots**:
  `projects/<project>/<page>/<YYYY-MM-DD>/index.html`
- `projects/<project>/<page>/index.html` is a redirect stub pointing at the
  newest snapshot — update its target when adding a new snapshot, so the
  undated URL always serves the latest version while old snapshots stay live.
- Pages are plain, self-contained HTML (inline CSS/JS, no build step, no
  external assets). Wrap imported fragments in a full document
  (`<!doctype html>`, `<meta charset>`, `<meta viewport>`) before committing.

## Current pages

| Project | Page | Latest snapshot |
|---|---|---|
| [will-chem](https://github.com/bchasnov/will-chem) | `will-chem/inbox/` — will-chem feedback inbox (bundles rendered reference docs under `.../<date>/docs/`) | 2026-07-21 |

Snapshots: `2026-07-20` (round 1) and `2026-07-21` (round 2 — receipts + aim
deck, with an expanded reference-docs set grouped into "Iteration 2" and
"Round 1 reference"). The `will-chem/inbox/` URL redirects to the newest.

Reference docs are rendered from the will-chem repo's markdown to styled HTML at
import time: export the `.md` sources, run them through python-markdown with the
`extra` extension, and wrap the output in the inbox theme. Raw `.md` sources are
kept alongside under `docs/src/`. Imported content is anonymized (individuals'
names → role labels, locations and biographical/career framing removed) before
committing.
