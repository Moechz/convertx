# NOTICE — third-party components and attributions

This package (`convertxdocker`, TOS 7 Docker application, version `0.20.0-1`) is
an independent packaging by Moechz of the upstream **ConvertX** project.

## 1. Upstream application — ConvertX

- Project: <https://github.com/C4illin/ConvertX>
- Version: **0.20.0** (released 2026-10-28; the latest upstream release at the
  time of this package)
- Licence: **AGPL-3.0** — <https://github.com/C4illin/ConvertX/blob/main/LICENSE>
- Relationship: the upstream application is used **unmodified**, inside the
  container image. No upstream source code is redistributed in this package, and
  no upstream file is modified. The complete corresponding source is published
  by the upstream project at the link above.

## 2. Container image — `c4illin/convertx:v0.20.0`

- Registry: Docker Hub — `docker.io/c4illin/convertx:v0.20.0`
- Multi-arch manifest digest:
  `sha256:6259e0c1e063120b1ddbc6937372a83be58182b2c9dc82264f58e100907b3e51`
  (linux/amd64 and linux/arm64)
- Maintainer: C4illin (the upstream project's own image)
- The image is pulled from Docker Hub at install time and is not redistributed
  here.

## 3. Components inside the image

The image is built by its maintainer on `debian:testing-slim` and bundles the
conversion tools it drives — among them ImageMagick, GraphicsMagick, ffmpeg,
Inkscape, resvg, libjxl, libheif, LibreOffice, Pandoc, Calibre, Ghostscript,
TeX Live, Poppler, DjVuLibre, dasel, assimp and `markitdown`. Their licences are
documented in the image's own package metadata; they are shipped inside the image
only and are not redistributed as separate files by this package.

## 4. This packaging

The files authored for this package — `docker-compose.yml`, `config.ini`,
`convertxdocker.lang`, `convertxdocker.svg`, build scripts and this
documentation — are released under **MIT** (see `LICENSE`).

## 5. Trademarks

“ConvertX”, “TerraMaster” and “TOS” are trademarks or registered trademarks of
their respective owners. Their use here is descriptive only. This package is
**not affiliated with, sponsored or endorsed by** the ConvertX project or
TerraMaster.

## 6. Icon

The application icon in `src/convertxdocker.svg` is a generic “file conversion”
mark drawn for this package; it does not reproduce the upstream ConvertX logo.

## 7. Contact

Moechz — moechz@163.com — <https://www.moechz.com/>
