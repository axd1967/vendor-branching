# Vendor Provenance: <Artefact Name>

- **Upstream URL:** <https://example.com>
- **Version / Tag:** <v1.0.0>
- **Upstream Commit / Checksum:** `<sha1-or-sha256-hash>`
- **License:** <SPDX-License-Identifier, e.g., MIT, Apache-2.0, GPL-3.0-only>
- **Vendor Branch:** `vendor/<name>`
- **Last Imported:** <YYYY-MM-DD>

## Update Procedure
1. Switch to the isolated vendor branch: `git checkout vendor/<name>`
2. Clear existing vendored files (keep `VENDOR.md`): `git ls-files -z | grep -zv 'VENDOR.md' | xargs -0 rm -f`
3. Unpack the new upstream release into `vendor/<name>/` (strip version numbers from folder names).
4. Update the version, checksum, and date fields above.
5. Stage, commit, and tag the clean drop:
   ```bash
   git add -A
   git commit -m "Import <name> <new-version>"
   git tag vendor/<name>/<new-version>
   ```
6. Merge `vendor/<name>` into your working branch and resolve any local patch conflicts.

## Local Modifications
- None (clean upstream drop).
<!-- If modified after merging into main, list custom patches here so they are preserved during future updates:
- `src/file.c`: Patched timeout constant for embedded target.
-->

