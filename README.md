# templates-registry

A Templates registry for [DeskStride](https://zelucode.com/deskstride) — the
Workflows page's Templates gallery **Browse** tab fetches `index.json` from
here (once you point Settings → Templates → Registry index URL at it).

## Layout

```
templates-registry/
├── templates/
│   └── <id>/               one folder per template; the folder name is the template id
│       ├── entry.json      the metadata, scanned to build index.json
│       ├── workflow.json   the downloadable raw workflow JSON the entry points at
│       ├── README.md       optional guide shown in the app's Details view
│       └── ...             anything else that belongs to this template
└── index.json              generated -- do not hand-edit, see "Adding a template" below
```

An entry in `templates/<id>/entry.json` follows this shape:

```json
{
  "id": "lowercase-hyphen-slug",
  "name": "Display Name",
  "author": "Optional author name",
  "description": "One or two sentences.",
  "category": "Optional category label",
  "tags": ["Optional", "Tags"],
  "downloadUrl": "https://raw.githubusercontent.com/zelucode/templates-registry/main/templates/<id>/workflow.json",
  "readmeUrl": "https://raw.githubusercontent.com/zelucode/templates-registry/main/templates/<id>/README.md",
  "sha256": "sha256 of the exact bytes at downloadUrl, lowercase hex",
  "minAppVersion": "0.14.0",
  "platforms": ["windows", "macos", "linux"],
  "attended": false,
  "requiresInternet": false,
  "nodeTypes": ["condition", "run_command"],
  "verified": true,
  "revoked": false,
  "revokedReason": null
}
```

`platforms`, `attended`, `requiresInternet`, `nodeTypes` (and `requiresExtensions`) are optional
and tell DeskStride what a template needs: an entry that needs a newer app (`minAppVersion`) or another
OS (`platforms`) is hidden from, and refused for, people it can't work for. An omitted field means "not
declared", so only fill what you have checked. Derive `nodeTypes` from the workflow file and `platforms`
from the node platform matrix; see DeskStride's template docs ("Entry metadata").

`downloadUrl` must point at the exact bytes hashed into `sha256` — the app
hard-fails the install if they don't match, no override. A template is a
plain workflow file — data, not code — so there's no signing step here,
unlike extensions.

## Adding a template

With DeskStride's template tool (`template-cli/deskstride_template_cli.py` in the DeskStride
repo; see its README):

1. `python deskstride_template_cli.py new <this-repo> <id> --name "..." --category "..."`
   scaffolds `templates/<id>/` with `workflow.json`, `entry.json` and a `README.md` stub. The URLs are
   copied from the other entries and `minAppVersion` defaults to the DeskStride version you run it from.
2. Build the workflow in DeskStride and export it over `templates/<id>/workflow.json`.
3. `python deskstride_template_cli.py sync <this-repo>` refreshes `sha256` and `nodeTypes`
   from the file (use `--check` in CI to fail when they are stale).
4. In the entry, set `platforms`, `attended` and `requiresInternet` (only what you have
   checked; see DeskStride's "Entry metadata" docs) and set `minAppVersion` to the oldest
   DeskStride that has every node the workflow uses.
5. `python deskstride_template_cli.py build-index <this-repo>` rebuilds `index.json`. It
   lists undeclared fields as advisories and rejects an entry whose `sha256` no longer
   matches its file.
6. Commit and push the entry, the file and the regenerated `index.json`.

By hand, the same steps are: put the workflow in `templates/<id>/workflow.json`, hash it with
`sha256sum templates/<id>/workflow.json`, write `templates/<id>/entry.json` with that `sha256` and the raw GitHub
URL in `downloadUrl` (`https://raw.githubusercontent.com/zelucode/templates-registry/main/templates/<id>/workflow.json`),
then rebuild the index.

The README is optional and display-only (it is not covered by `sha256`): the app fetches it from
`readmeUrl` only when someone opens Details. Use absolute https links; images and relative links are not loaded.

## Revoking a template

Set `"revoked": true` (and optionally `"revokedReason"`) on the entry in
`templates/<id>/entry.json`, rebuild the index, and push. The app never
auto-removes anything already imported by a user — revocation only blocks
future installs from the Browse tab.
