# Packaged MVP 1 archives

These files are the official MVP 1 zips from git tag `mvp-1` (commit `81d25cc`), stored as base64 because this repository was published through a text-only GitHub API.

- `fashion-beach-mvp-dist.zip.b64` — playable static build (same files GitHub Pages serves)
- `fashion-beach-mvp-source.zip.b64` — complete source and assets (no `node_modules`)

To unpack locally:

```bash
base64 -d fashion-beach-mvp-dist.zip.b64 > fashion-beach-mvp-dist.zip
base64 -d fashion-beach-mvp-source.zip.b64 > fashion-beach-mvp-source.zip
unzip fashion-beach-mvp-dist.zip -d dist
unzip fashion-beach-mvp-source.zip
```
