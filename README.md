# Dye Bioavailability

[Zarrcade](https://github.com/JaneliaSciComp/zarrcade)-based browser for the LavisLab dye bioavailability image collection.

Live site (after Pages is enabled): <https://janeliascicomp.github.io/dyebioavailability-website/>

## What's in here

```
.
├── index.html       # Zarrcade SPA entry point (prebuilt)
├── config.json      # Site configuration: title, columns, filters, viewers, branding
├── assets/          # Hashed JS/CSS/font bundles
├── icons/           # Viewer icons + fallback thumbnail
├── content/         # Curator-managed assets served via HTTP (see below)
│   ├── zarrcade.csv     # Image manifest
│   └── branding/        # Site-specific logos (T3, HHMI Janelia)
└── .github/workflows/pages.yml   # CI deploy to GitHub Pages
```

### How `content/` is served

`content/` is the canonical, version-controlled mirror of everything the SPA
loads at runtime (CSV + branding images). It is **not** served by GitHub Pages
— the SPA fetches it over HTTP from a separate static host (currently S3),
**relative to `dataUrl`** in `config.json`.

The contract is: the directory containing `dataUrl` on the host must match
the layout of `content/` here. Today `dataUrl` is

  <https://s3.janelia.org/lavis-lab/Dye_Bioavailability_images/Website/zarrcade.csv>

so the SPA will look for branding logos at

  https://s3.janelia.org/lavis-lab/Dye_Bioavailability_images/Website/branding/T3-Logo.png

and so on. Anything new you drop into `content/` must be uploaded to the
same S3 prefix (`s3://lavis-lab/Dye_Bioavailability_images/Website/`) for
the live site to see it.

## Updating the site

- **New columns / filters / branding tweaks:** edit `config.json` and push to
  `main`. The Pages workflow redeploys automatically.
- **New data or branding assets:** add the files under `content/`, commit,
  AND upload the same files to `s3://lavis-lab/Dye_Bioavailability_images/Website/`.
  The repo and S3 must stay in sync; commits alone won't reach the SPA.
- **Newer Zarrcade release:** re-run
  `npx @janelia/zarrcade init <tmp>` against the new package version,
  then copy `<tmp>/{index.html,assets,icons}` over the files here. Leave
  `config.json`, `content/`, and the workflow in place.

## Local preview

```bash
npx serve .
# open http://localhost:3000 — the SPA will fetch the live CSV from S3.
```

If you want to develop against a local CSV, drop it next to `config.json`
and add a `config.local.json` (gitignored) overriding `dataUrl`:

```json
{ "dataUrl": "./local-snapshot.csv" }
```

## Enabling GitHub Pages

One-time setup on github.com → repository **Settings → Pages**:

- **Source:** "GitHub Actions" (not "Deploy from a branch").

Once that's set, every push to `main` runs `.github/workflows/pages.yml`
and the site goes live at the URL above within a minute.
