# Packaged MVP 1 archives

These files are the official MVP 1 zips from git tag `mvp-1` (commit `81d25cc`), stored as split base64 parts because this repository was published through a text-only GitHub API with a request-size limit.

- `fashion-beach-mvp-dist.zip.b64.part*` — playable static build (same files GitHub Pages serves)
- `fashion-beach-mvp-source.zip.b64.part*` — complete source and assets (no `node_modules`)

To unpack locally:

```bash
cat fashion-beach-mvp-dist.zip.b64.part* | base64 -d > fashion-beach-mvp-dist.zip
cat fashion-beach-mvp-source.zip.b64.part* | base64 -d > fashion-beach-mvp-source.zip
unzip fashion-beach-mvp-dist.zip -d dist
unzip fashion-beach-mvp-source.zip
```
