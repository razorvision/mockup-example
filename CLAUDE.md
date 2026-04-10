# CLAUDE.md — Northwind Coffee Roasters Mockup

You are working on the **Northwind Coffee Roasters** website mockup. Read this file before editing anything.

The authoritative standards for this repo live in the [Razorvision Mockup & UX Delivery Best Practices](https://razorvision.github.io/mockup-best-practices/). This file is the project-specific guide that sits on top of those ADRs.

## Current state

- **Version:** v3
- **Delivered:** May 26, 2026
- **Live URL:** https://razorvision.github.io/mockup-example/
- **Latest archive:** https://razorvision.github.io/mockup-example/versions/v3/

## Branching (ADR03)

- `main` is always what is published to the customer. **Never commit to `main` directly.**
- Each iteration goes on its own branch: `iter/v4-<short-topic>`. Branch from the latest `main`.
- When the iteration is ready to deliver, perform the version bump on the branch (ADR04), then merge to `main`.

## Delivery sequence (ADR04)

Run these steps on the iteration branch when a version is ready to ship:

1. Update the version stamp on **every page** (see "Version stamp" below).
2. Create the snapshot:
   ```sh
   mkdir -p versions/v{N}
   rsync -av --delete \
     --exclude='.git/' \
     --exclude='.github/' \
     --exclude='versions/' \
     --exclude='README.md' \
     --exclude='CLAUDE.md' \
     --exclude='.gitignore' \
     --exclude='.nojekyll' \
     ./ versions/v{N}/
   ```
3. Update `README.md` and this file with the new version + date + archive URL.
4. Commit: `git commit -m "Deliver v{N}"`.
5. Merge to `main`: `git checkout main && git merge --no-ff iter/v{N}-<topic>`.
6. Tag: `git tag v{N}.0 && git push origin v{N}.0`.
7. Push `main`: `git push origin main` — this triggers the Pages deploy (ADR06).

## Version stamp (ADR05)

Every page carries the same footer stamp. The string to update on each delivery:

```html
<footer class="version-stamp">
  Northwind Coffee Roasters · v3 · May 26, 2026 · Razorvision
</footer>
```

Files that contain the stamp (update all of them on every delivery):

- `index.html`
- `pages/about.html`
- `pages/menu.html`
- `pages/wholesale.html`
- `pages/contact.html`

Snapshots under `versions/v{N}/` keep their **original** stamp — never rewrite a snapshot to show a newer version.

## Structure (ADR07)

```
/                       ← latest version (currently v3)
├── index.html
├── pages/{about,menu,contact}.html
├── assets/{css,js,images}/
└── versions/v{1,2,3}/  ← frozen snapshots
```

All asset and inter-page links use **relative paths** (ADR07, ADR09). Never `/assets/...` — use `assets/...` from root and `../assets/...` from inside `pages/`. Absolute paths break inside `versions/v{N}/`.

## Build (ADR08)

Pure static. Tailwind via the Play CDN. Google Fonts via `<link>`. **No** `package.json`, no compiler, no JSX.

## Brand

- **Customer:** Northwind Coffee Roasters (Portland, OR)
- **Tagline:** "Small-batch coffee, roasted in Portland."
- **Colors:**
  - `--coffee` `#6B4423` (deep brown, primary)
  - `--ember` `#C97B3F` (orange-amber, accent)
  - `--cream` `#F5EFE6` (background)
  - `--ink`   `#2A1F1A` (body text)
- **Type:**
  - Headings: `Fraunces` (serif)
  - Body: `Inter`
- **Voice:** warm, considered, never twee. No "artisanal" or "curated" — use "small-batch", "single-origin", "hand-roasted".

## Decisions already made (preserve across iterations)

- Hero is photo-led, full-bleed, with the headline overlay top-left.
- Pricing on the menu page is always shown in USD with the cents (`$18.00`, not `$18`).
- Testimonials use first name + city only (no last names — customer privacy preference).
- No carousels. Static grids only — customer specifically asked for this in the v1 review.
