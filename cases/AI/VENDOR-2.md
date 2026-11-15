# Vendor Provenance: <Artefact Name>

- **Upstream URL:** <https://example.com>
- **Source Type:** <Git (`git subtree`) | Alien Archive (tarball/zip/raw)>
- **Version / Tag:** <v1.0.0>
- **Upstream Commit / Checksum:** `<sha1-or-sha256-hash>`
- **License:** <SPDX-License-Identifier, e.g., MIT, Apache-2.0>
- **Vendor Branch:** `vendor/<name>`
- **Last Imported:** <YYYY-MM-DD>

## Update Procedure
1. Switch to `vendor/<name>` and clear tracked files safely:
   `git ls-files -z vendor/<name>/ | grep -zv 'VENDOR\.\(md\|sh\)' | xargs -0 rm -f`
2. Import the new release (or run `./vendor/<name>/VENDOR.sh` if present):
   - **Git upstream:** `git subtree pull --prefix=vendor/<name> <url> <ref>`
   - **Alien upstream:** Extract archive into `vendor/<name>/` and strip version numbers from folder names.
3. Commit and tag on the vendor branch:
   `git add -A && git commit -m "Import <name> <new-version>" && git tag vendor/<name>/<new-version>`
4. Switch to a task branch (`git switch -c feat/update-<name>`), merge `vendor/<name>`, update this `VENDOR.md`, and resolve conflicts.
5. Merge the task branch into `main` and push `main`, `vendor/<name>`, and the new tag.

## Local Modifications
- None (clean upstream drop).

