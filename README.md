# templates-registry

A Templates registry for [Automation OS](https://github.com/) — the
Workflows page's Templates gallery **Browse** tab fetches `index.json` from
here (once you point Settings → Templates → Registry index URL at it).

See `docs/features/workflow-templates.md` in the main app repo for the full
"Browse / Registry" contract this repo implements.

## Layout

```
templates-registry/
├── templates/       one entry (metadata) per file, scanned to build index.json
│   └── <id>.json
├── files/           the actual downloadable raw workflow JSON each entry points at
│   └── <id>.json
└── index.json       generated -- do not hand-edit, see "Adding a template" below
```

An entry in `templates/<id>.json` follows this shape:

```json
{
  "id": "lowercase-hyphen-slug",
  "name": "Display Name",
  "author": "Optional author name",
  "description": "One or two sentences.",
  "category": "Optional category label",
  "tags": ["Optional", "Tags"],
  "downloadUrl": "https://.../raw/branch/main/files/<id>.json",
  "sha256": "sha256 of the exact bytes at downloadUrl, lowercase hex",
  "minAppVersion": "0.14.0",
  "verified": true,
  "revoked": false,
  "revokedReason": null
}
```

`downloadUrl` must point at the exact bytes hashed into `sha256` — the app
hard-fails the install if they don't match, no override. A template is a
raw workflow-JSON node graph (the same shape as this app's own
`workflows/examples/*.json` files) — data, not code, so there's no
signing step here, unlike the companion `extensions-registry`.

## Adding a template

1. Drop the raw workflow JSON into `files/<id>.json`.
2. Compute its hash: `python aos_template_cli.py sha256 files/<id>.json`
   (get `aos_template_cli.py` from `template-cli/` in the main app repo).
3. Write `templates/<id>.json` with that hash in `sha256` and the Gitea raw
   URL (`.../raw/branch/main/files/<id>.json`) in `downloadUrl`.
4. Rebuild the index: `python aos_template_cli.py build-index .`
5. Commit and push both the entry, the file, and the regenerated
   `index.json`.

## Revoking a template

Set `"revoked": true` (and optionally `"revokedReason"`) on the entry in
`templates/<id>.json`, rebuild the index, and push. The app never
auto-removes anything already imported by a user — revocation only blocks
future installs from the Browse tab.
