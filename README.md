# Dye Bioavailability

[Zarrcade](https://github.com/JaneliaSciComp/zarrcade)-based web site for the Lavis Lab's dye bioavailability image collection.

Live site: <https://dyebioavailability.janelia.org/>

## What's in here

```
.
├── index.html       # Zarrcade SPA entry point (prebuilt)
├── config.json      # Site configuration: title, columns, filters, viewers, branding
├── assets/          # Hashed JS/CSS/font bundles
├── icons/           # Viewer icons + fallback thumbnail (from @janelia/zarrcade)
├── branding/        # Site-specific logos (T3, HHMI Janelia) — deployed with the SPA
├── content/         # Version-controlled backup of the data served at dataUrl
│   └── zarrcade.csv
└── .github/workflows/pages.yml   # CI deploy to GitHub Pages
```

### About `content/`

`content/` is a **backup** of what's actually deployed at the `dataUrl`
location. The live site does *not* read from `content/` — it fetches over
HTTP from the URL in `config.json`:

  <https://s3.janelia.org/lavis-lab/Dye_Bioavailability_images/zarrcade.csv>

S3 is the source of truth for what users see. `content/` exists so the
repo carries a version-controlled snapshot of that data — useful for
diffing, recovery, and reproducibility — but uploading a new file to
`content/` alone does **nothing** for the live site until you also upload
it to `s3://lavis-lab/Dye_Bioavailability_images/`.

Branding images are different — they live in `branding/` at the repo
root and ship with the GitHub Pages bundle, so they don't need to be on
S3 at all.

## Updating the site

- **Config / filters / branding tweaks:** edit `config.json` (or files
  under `branding/`) and push to `main`. The Pages workflow redeploys
  automatically.
- **New data:** upload the regenerated `zarrcade.csv` to
  `s3://lavis-lab/Dye_Bioavailability_images/` AND update the backup at
  `content/zarrcade.csv` so the repo doesn't drift. The S3 upload is
  what users see.
- **Newer Zarrcade release:** re-run
  `npx @janelia/zarrcade init <tmp>` against the new package version,
  then copy `<tmp>/{index.html,assets,icons}` over the files here.
  Leave `config.json`, `branding/`, `content/`, and the workflow in
  place.

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

