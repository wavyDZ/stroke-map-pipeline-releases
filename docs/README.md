This folder is served by GitHub Pages and exists for one file: `index.json`,
the Blender extension repository listing.

Do not edit it by hand -- run `make_index.py` from the repo root, which drives
Blender's own `server-generate` so the schema stays correct, then repoints
`archive_url` at the published release asset.

Publish the release BEFORE regenerating. The index carries a sha256 of the
archive and Blender verifies it, so an index built against a zip that was
never uploaded fails at install rather than silently serving the wrong thing.
