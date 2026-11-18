# convertx-for-tos

> TerraMaster TOS 7 App Center — **Docker application** package for
> [ConvertX](https://github.com/C4illin/ConvertX).

## What this is

A TOS Docker application archive that installs ConvertX on TOS 7 through the App
Center (one archive per architecture):

- `convertxdocker_x86_64.tar.gz` — x86_64
- `convertxdocker_aarch64.tar.gz` — aarch64

Each archive holds exactly four files:

- `config.ini` — TOS application metadata (`application_type: docker`, id `convertxdocker`)
- `convertxdocker.lang` — 23-language superset (TOS requires 14)
- `convertxdocker.svg` — application icon (SVG, 73.4% glyph)
- `docker-compose.yml` — one service, image `c4illin/convertx:v0.20.0`

## How it works

| | |
|---|---|
| Container image | `c4illin/convertx:v0.20.0` (Docker Hub, upstream image used unmodified) |
| Open | `http://<NAS-IP>:8070/` (container port 3000) |
| First run | open `http://<NAS-IP>:8070/` and **create the first account immediately**, then log in |
| Runs as | non-root, `user: "1000:1000"`, `HOME=/home/convertx` |
| Persistent data | `/app/data` → `/Volume1/DockerAppData/convertxdocker/data` |
| Logs | `docker logs convertxdocker` (stdout/stderr) |

Environment (fixed in the package):

| Variable | Value | Why |
|---|---|---|
| `TZ` | `Asia/Shanghai` | correct timestamps |
| `HOME` | `/home/convertx` | LibreOffice, Calibre and fontconfig need a writable home when the container runs as uid 1000 |
| `HTTP_ALLOWED` | `true` | the upstream session cookie is `Secure` by default, which breaks login at a plain-HTTP `http://<NAS-IP>:8070` address; TOS always opens Docker apps over HTTP |
| `ACCOUNT_REGISTRATION` | `false` | the first account can still be created, but nobody else can register afterwards |

ConvertX converts images, documents, e-books, audio, video and data files between
more than a thousand format combinations, using the converters bundled in the
image (ImageMagick, ffmpeg, LibreOffice, Pandoc, Calibre, Inkscape, Ghostscript,
TeX Live and others). It supports batch conversion, multiple accounts and
password protection.

## Design highlights

- **Non-root.** `user: "1000:1000"`; the container reports uid 1000 on real hardware.
- **Writable `HOME`.** `/home/convertx` (created by the image for uid 1000) is set explicitly,
  because LibreOffice, Calibre and fontconfig need a writable home. Without it, document and
  e-book conversions can fail under a non-root user.
- **Fixed image tag, never `:latest`**, Docker Hub only, no `privileged`, no `network_mode: host`, no Docker socket, no `cap_add`.
- **Login works on the NAS.** `HTTP_ALLOWED=true` because the TOS App Center opens the app at a plain-HTTP LAN address and ConvertX would otherwise mark its session cookie `Secure` and loop at the login page.
- **No open registration.** `ACCOUNT_REGISTRATION=false`: the first account is yours, later self-registration is disabled.
- **Health check** with the image's own `curl` against `http://127.0.0.1:3000/`, 60 s start period.
- **Data survives restarts**; `restart: unless-stopped` brings the app back after a NAS reboot.
- **Deterministic build**: GNU tar with uid/gid 0 and zeroed timestamps, gzip without a timestamp.

## Runtime file manifest

Everything the application writes on the NAS lives inside its own data directory;
the container-internal `/tmp` and `/home/convertx` are discarded with the
container. The table lists what is created and how it behaves. Nothing else is
written.

| Path | Purpose | Created when | Growth bound | Lifecycle |
|---|---|---|---|---|
| `/Volume1/DockerAppData/convertxdocker/data` | ConvertX application data: uploaded files, converted output, SQLite database (accounts, history, settings) | on first start / on demand | bounded by the files you convert | persistent; converted files are auto-deleted after 1 hour by default, the rest is kept on uninstall unless you ask to delete it |

The application logs to stdout/stderr only (`docker logs convertxdocker`); it does
not write log files on the NAS. No password, token or other credential is printed
by the package or by the upstream image.

## Privacy

This application runs entirely on your NAS. **The developer does not collect,
receive or have access to any data processed by the application.** There is no
telemetry, no analytics and no cloud service. Files are converted locally and are
never sent to the developer or to a third-party conversion service.

| Topic | Detail |
|---|---|
| Data processed | The files you upload and convert, the conversion history, and your local ConvertX accounts and settings. |
| Where it is stored | `/Volume1/DockerAppData/convertxdocker/data` on your NAS. Nothing is uploaded to the developer. |
| Retention period | Converted files are deleted by the app after **1 hour by default**; history and accounts stay until **you** delete them. Uninstalling keeps the data unless you tick “delete data”. |
| Your rights | **Access / export**: download the result from the History tab, or copy the data folder. **Correct**: edit the account and settings in the UI. **Delete**: delete the job in the History tab, uninstall with “delete data”, or remove the folder. |
| Deletion channel | Delete the job or the folder yourself, or email **moechz@163.com** (answered within 30 days). |
| Security measures | Container runs as uid 1000 (never root) with a dedicated writable HOME, no `privileged`, no host network, no `cap_add`, no Docker socket, one published port; image tag pinned. |

**Full privacy policy:** [PRIVACY.md](PRIVACY.md) —
<https://github.com/Moechz/convertx/blob/main/PRIVACY.md> — and the website
version at <https://www.moechz.com/privacy.html>. The package `help` field points
to `PRIVACY.md`.

## Third-party services (API disclosure)

The developer operates no server in this path, and the application calls **no
third-party API**. Conversions run in the container on your NAS.

| Service | Endpoint | Data sent | Destination / region |
|---|---|---|---|
| Docker image pull | `registry-1.docker.io` or your registry mirror | anonymous pull | Docker Hub, US / your mirror |

There is no update check and no telemetry. Disable Docker Hub access entirely by
pre-pulling the image from a mirror if you do not want the pull request.

## Licence and attributions

| Component | Licence | Notes |
|---|---|---|
| ConvertX | AGPL-3.0 | <https://github.com/C4illin/ConvertX> — upstream application, used unmodified inside the image |
| `c4illin/convertx:v0.20.0` image | AGPL-3.0 | Docker Hub image of the upstream release, pulled at install time |
| This packaging (compose file, metadata, translations, icon) | MIT | see `LICENSE` |

“ConvertX”, “TerraMaster” and “TOS” are trademarks of their respective owners.
This is an independent community package by Moechz; it is not affiliated with or
endorsed by the ConvertX project or TerraMaster. The icon is an original
“file conversion” mark and does not reproduce the upstream logo.

Full details: [NOTICE.md](NOTICE.md).

## Build

```bash
scripts/build-dual.sh              # both architectures -> out/convertxdocker_{x86_64,aarch64}.tar.gz
scripts/build.sh 0.20.0-3          # single iteration bump (x86_64)
scripts/build.sh 0.20.0-2 aarch64  # one architecture only
```

The build stages the four files, substitutes the version/platform placeholders,
removes CRLF/BOM/AppleDouble, then runs `tools/verify.py`, a review-standards
self-check (archive layout, JSON validity, required fields, 14+ language
coverage, version consistency, Docker-Hub-only images, fixed tag,
reserved/recommended host ports, non-root user, per-service healthcheck/TZ/
restart, data path below the application data root, never the `/Volume*`
wildcard, comment-free sequence blocks, `x-app-meta` position, no literal
secrets and the SVG size/element limits).

## Local verification

```bash
./TOSAppSelfTestingTool -c out/convertxdocker_x86_64.tar.gz    # official spec check
./TOSAppSelfTestingTool -c out/convertxdocker_aarch64.tar.gz
bash test/install-and-verify.sh                  # on-device E2E (needs root)
```

> Docker applications cannot be side-loaded through the App Center's manual
> install page (it accepts `.deb` only); the official self-testing tool's `-i`
> mode or `docker compose` are the only ways to install one for testing.

## Store submission checklist

1. Public GitHub repo (code + README only).
2. Release **tag `v0.20.0-2`** with four assets:
   `convertxdocker_x86_64.tar.gz` + `.sha256`,
   `convertxdocker_aarch64.tar.gz` + `.sha256`.
3. Developer platform: two App records with the same ID `convertxdocker`, package
   type Docker, architectures `x86_64` and `aarch64`.
4. Version Management → Add Version `0.20.0-2` for each architecture →
   automated validation → review.

## Limitations

- The application runs inside a container; data lives in the application data
  directory (`/Volume1/DockerAppData/convertxdocker/`) as described above.
- Converted files are removed automatically after 1 hour by default
  (`AUTO_DELETE_EVERY_N_HOURS`); download them or change the setting if you need
  them longer.
- The web UI is plain HTTP on the LAN and the session cookie is allowed over
  HTTP (`HTTP_ALLOWED=true`) so login works from the NAS address; put it behind a
  TLS reverse proxy before exposing it to the internet, and protect the first
  account with a strong password.
- Self-registration is closed after the first account. If you lose that account,
  remove the data folder (or use a new one) to create a fresh first account.
- `JWT_SECRET` is not set in the package, so ConvertX generates a random signing
  key on each start: a container restart logs users out (accounts and data are
  kept). Set your own `JWT_SECRET` if that matters to you.
- Docker applications cannot be updated in place by the App Center; to upgrade,
  uninstall and reinstall — the data folder is kept by default.

## Support

- Issues and questions: <https://github.com/Moechz/convertx/issues>
- Email: moechz@163.com
- Privacy: [PRIVACY.md](PRIVACY.md) · Licence: [NOTICE.md](NOTICE.md)

---

Packaged for TOS 7 by Moechz. Upstream image used unmodified from Docker Hub.
