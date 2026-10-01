# templates-registry

A Templates registry for [DeskStride](https://zelucode.com/deskstride) — the
Workflows page's Templates gallery **Browse** tab fetches `index.json` from
here (once you point Settings → Templates → Registry index URL at it).

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
  "downloadUrl": "https://raw.githubusercontent.com/zelucode/templates-registry/main/files/<id>.json",
  "sha256": "sha256 of the exact bytes at downloadUrl, lowercase hex",
  "minAppVersion": "0.14.0",
  "verified": true,
  "revoked": false,
  "revokedReason": null
}
```

`downloadUrl` must point at the exact bytes hashed into `sha256` — the app
hard-fails the install if they don't match, no override. A template is a
plain workflow file — data, not code — so there's no signing step here,
unlike extensions.

## Adding a template

1. Drop the raw workflow JSON into `files/<id>.json`.
2. Compute the SHA-256 of that exact file (lowercase hex), for example
   `sha256sum files/<id>.json`.
3. Write `templates/<id>.json` with that hash in `sha256` and the raw GitHub
   URL (`https://raw.githubusercontent.com/zelucode/templates-registry/main/files/<id>.json`) in `downloadUrl`.
4. Rebuild `index.json` from the entries in `templates/` with DeskStride's
   template tool (`build-index`).
5. Commit and push both the entry, the file, and the regenerated
   `index.json`.

## Revoking a template

Set `"revoked": true` (and optionally `"revokedReason"`) on the entry in
`templates/<id>.json`, rebuild the index, and push. The app never
auto-removes anything already imported by a user — revocation only blocks
future installs from the Browse tab.
