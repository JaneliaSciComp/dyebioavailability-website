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
├── ext/             # Site-specific branding logos (T3, HHMI Janelia)
└── .github/workflows/pages.yml   # CI deploy to GitHub Pages
```

The dataset itself (`zarrcade.csv`) lives on S3 at
<https://s3.janelia.org/lavis-lab/Dye_Bioavailability_images/zarrcade.csv>
and is fetched by the browser at load time. It is **not** committed to this
repo — `.gitignore` excludes `*.csv`.

## Updating the site

- **New columns / filters / branding tweaks:** edit `config.json` and push to
  `main`. The Pages workflow rebuilds and redeploys automatically.
- **New data:** re-upload the regenerated `zarrcade.csv` (and any new
  thumbnails) to S3. No code change needed — the SPA fetches it fresh on
  every page load.
- **Newer Zarrcade release:** re-run
  `npx @janelia/zarrcade init <tmp>` against the new package version,
  then copy `<tmp>/{index.html,assets,icons}` over the files here. Leave
  `config.json`, `ext/`, and the workflow in place.

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
