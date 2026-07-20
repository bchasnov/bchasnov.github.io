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
| [will-chem](https://github.com/bchasnov/will-chem) | `will-chem/inbox/` — UMA campaign feedback inbox | 2026-07-20 |
