# Repository Agent Instructions

## Third-Party Artefact & Vendor Branching Policy

- **Package Manager Exception:** Use the project's native package manager and lockfile (e.g., `npm`, `cargo`, `pip`, `go mod`, `composer`) whenever a third-party dependency is available there. Do not manually vendor managed packages.
- **Vendor Branching Requirement:** For any third-party artefact not managed by a package manager (e.g., standalone source files, tarballs, ZIP archives, external repositories, or documentation), you **must** use the vendor branching workflow. Never copy-paste external artefacts directly into `main` or feature branches.
- **Isolated Vendor Branches:** Import and update raw third-party artefacts exclusively on an isolated branch named `vendor/<name>` (created via `git checkout --orphan vendor/<name>` or a dedicated worktree).
- **Provenance Metadata:** Every vendor drop must include or update a `VENDOR.md` file inside its target directory (`vendor/<name>/VENDOR.md`) recording the upstream URL, version/commit hash, license, and exact import commands.
- **Tagging & Merging:** Tag every clean vendor drop as `vendor/<name>/<version>` on the vendor branch before merging it into the working branch. Keep custom local patches separate from raw vendor import commits.

