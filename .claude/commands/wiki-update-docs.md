Update raw DataJoint docs from upstream and refresh stale wiki pages.

Usage: /wiki-update-docs

Steps:

1. Sparse-clone only the `src/` directory from the upstream docs repo into a temp directory:

   ```bash
   git clone --depth=1 --filter=blob:none --sparse \
     https://github.com/datajoint/datajoint-docs.git /tmp/dj-docs-update
   git -C /tmp/dj-docs-update sparse-checkout set src
   ```

2. List the .md files in `/tmp/dj-docs-update/src/`. Compare against what's currently in `raw/`
   and report which files are new vs already present.

3. Copy all .md files from `/tmp/dj-docs-update/src/` into `raw/`, overwriting existing files.

4. Remove the temp directory:

   ```bash
   rm -rf /tmp/dj-docs-update
   ```

5. Run the refresh tool to detect changed files and re-ingest them:

   ```bash
   python tools/refresh.py
   ```

   This compares SHA256 hashes of raw files against a cache and re-ingests any that changed,
   updating the corresponding wiki/sources/ pages.

6. Report a summary: how many raw files were synced (new + updated), how many wiki pages
   were refreshed by tools/refresh.py.
