# Privacy Policy — ConvertX for TerraMaster TOS (`convertxdocker`)

**Last updated:** 18 November 2026
**Data controller / contact:** Moechz — moechz@163.com
**Applies to:** version `0.20.0-2` and later of the TOS application
`convertxdocker`, the container image it runs and this repository.

## 1. Summary

- The app runs **entirely on your NAS**. The developer does not receive, collect
  or have access to your data.
- No telemetry, no analytics, no account with the developer and no cloud service.
- **No file ever leaves the NAS.** The conversion is done locally by the tools
  bundled in the container image.
- The only network request the package makes is the anonymous Docker image pull
  from Docker Hub (or your mirror).

## 2. What data is processed

| Data | Stored on the NAS at |
|---|---|
| Files you upload for conversion | `/Volume1/DockerAppData/convertxdocker/data` |
| Converted output files | `/Volume1/DockerAppData/convertxdocker/data` |
| Conversion history (file names, formats, size, date, status) | the app's SQLite database, same folder |
| Local accounts: user name, password hash, session data | the app's SQLite database, same folder |
| Application settings | the app's SQLite database, same folder |
| Container logs | stdout/stderr only — `docker logs convertxdocker`; no log file on the NAS |

The app creates the first local account when you open it; the password is stored
only as a hash in your local database and is never transmitted anywhere. Nothing
is uploaded to the developer or to any third party.

## 3. Retention period

- **Converted files:** deleted automatically by the application after **1 hour by
  default** (upstream `AUTO_DELETE_EVERY_N_HOURS=1`). Set it to another value, or
  to `0` to disable the automatic cleanup, in the container environment.
- **Uploaded source files and the conversion record:** kept until **you** delete
  them in the History tab.
- **Accounts, settings and history database:** kept until **you** delete them.
  There is no automatic expiry and no upload. Uninstalling the app keeps the data
  unless you tick “delete data”.
- **Logs:** stdout/stderr only; the Docker daemon keeps them according to its own
  rotation settings.

## 4. Your rights and the deletion channel

| Right | How |
|---|---|
| Access / export | Download the converted result from the History tab, or copy the folder `/Volume1/DockerAppData/convertxdocker/` — it is the complete state. |
| Correct | Edit the account and settings in the app's own web UI. |
| Delete | Delete a job (and its files) in the **History** tab, uninstall the app with “delete data”, or stop the container and remove `/Volume1/DockerAppData/convertxdocker/`. |
| Assistance | Email **moechz@163.com** with the subject “ConvertX data request”; a reply follows within 30 days. |

The developer holds no copy of your data, so deletion is normally completed by
removing the files on your own NAS; the email channel exists for questions and
for requests about the website or the publishing records.

## 5. Security measures

- The container runs as **uid 1000**, never root; no `privileged`, no `cap_add`,
  no host network, no Docker socket.
- Only the application's own port is published (host 8070 → container 3000).
- `HOME` is set to the image's unprivileged home (`/home/convertx`), so the
  bundled converters do not need root-writable paths.
- The image tag is **pinned**; the package is updated when upstream ships
  security fixes.
- Data stays inside the application data folder, owned by the app user.
- The first account you create protects the web UI. **Self-registration is
  disabled** in the package (`ACCOUNT_REGISTRATION=false`), so nobody else can
  create an account afterwards.
- The session cookie is allowed over plain HTTP (`HTTP_ALLOWED=true`) because the
  TOS App Center opens Docker applications at an `http://<NAS-IP>:<port>` address;
  without it, login does not work. On a LAN this is the normal TOS model, but the
  cookie is not TLS-protected: put the app behind a TLS reverse proxy before
  exposing it outside your LAN.
- `JWT_SECRET` is not fixed in the package, so the signing key is regenerated on
  every container start; this invalidates sessions (not accounts or data) after a
  restart.

## 6. Third-party sharing and API disclosure

The developer shares nothing, and the application calls **no third-party API**.

| Service | Endpoint | Data sent | Destination / region |
|---|---|---|---|
| Docker image pull | `registry-1.docker.io` or your registry mirror | anonymous pull — no account, no user data | Docker Hub, US / your mirror |

There is no update check, no telemetry and no external converter service: every
conversion runs in the container on your NAS. No data is sold or shared with
advertisers.

## 7. Children

The app is a self-hosted utility and is not directed at children.

## 8. Changes

Material changes are published in this file and reflected in the “Last updated”
date. The policy also ships with every release of the package.

## 9. Contact

Moechz — **moechz@163.com** — <https://www.moechz.com/privacy.html>
