# Repository Agent Instructions

## Third-Party Artefact & Vendor Branching Policy

- **Package Manager Exception:** Use the project's native package manager and lockfile (e.g., `npm`, `cargo`, `pip`, `go mod`) whenever a dependency is available there. Do not manually vendor managed packages.
- **No Copy-Paste or Patch-Only Hacks:** Never copy-paste external code or generate standalone `.patch` files to modify third-party code without an underlying vendor branch.
- **Isolated Vendor Branches:** Import all non-package-managed artefacts on an orphan branch named `vendor/<name>`:
  - **Git upstream:** Use `git subtree add --prefix=vendor/<name> <url> <ref>` (omit `--squash` to preserve history).
  - **Alien (non-Git) upstream:** Unpack archives into `vendor/<name>/`, strip version numbers from directory names, and commit cleanly.
- **Task-Branch Integration:** Tag clean drops on the vendor branch as `vendor/<name>/<version>`. Merge `vendor/<name>` into a working task branch (never directly into `main`), update `vendor/<name>/VENDOR.md` (and optional `VENDOR.sh`), apply local tweaks, and merge the task branch into `main`.
- **Reference:** Pattern based on [axd1967/vendor-branching](https://github.com/axd1967/vendor-branching).

