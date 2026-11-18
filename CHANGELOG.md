# Changelog

All notable user-visible changes to the ConvertX TOS application package.

## 0.20.0-1 — 2026-11-18

Remediation of the conditional-approval review of `0.17.0-2`, plus an upstream
upgrade.

### Changed
- Upgraded the container image to **`c4illin/convertx:v0.20.0`** (the latest
  upstream release, 2026-10-28): security and converter fixes, new DjVu support,
  faster start-up. Same ports, data path and non-root user.
- Set `HOME=/home/convertx` so the bundled converters (LibreOffice, Calibre,
  fontconfig) have a writable home when the container runs as uid 1000. Without
  it, document and e-book conversions could fail under the non-root user.
- Health check switched from the image's old `bun -e` probe to `curl`, because
  the 0.20.0 image ships a compiled binary and no longer includes Bun.
- Rewrote the description (English and Chinese) to state the features actually
  delivered instead of marketing shorthand; removed the emoji from `app.lang`.

### Added
- **Privacy policy** (`PRIVACY.md`), linked from the package `help` field and
  summarised in the README: data stored, retention (converted files are deleted
  after 1 hour by default), user rights, security measures, the deletion channel
  and the third-party disclosure (no third-party API; only the Docker Hub pull).
- **Licence notice** (`NOTICE.md`): upstream ConvertX (AGPL-3.0, used unmodified),
  the Docker Hub image and its multi-arch digest, and the MIT licence of this
  packaging.
- README sections for privacy, third-party services, licence and support, and the
  dual-architecture asset names.

### Fixed
- `help` no longer points at the issue tracker but at the privacy policy, so the
  policy can be opened from the App Center (review items C3/C5).
- Removed stale build metadata from `tosapp.json` (a probe block from the
  0.17.0 image and an `entrypoint_file` that does not exist).

## 0.17.0-2 — 2026-11-15

### Changed
- Application icon normalised to the TOS store grid: the rounded tile fills the
  512 px canvas and the glyph is centred at 73.4% with 68 px padding.

## 0.17.0-1 — 2026-11-11

### Added
- First release. ConvertX (`c4illin/convertx:v0.17.0`, Docker Hub official image,
  unmodified) packaged as a TOS 7 Docker application, published on port 8070 and
  running as a non-root user (1000:1000).
- Persistent data is kept under `/Volume1/DockerAppData/convertxdocker/`.
