# SuperSearch

A local-first desktop browser with Chromium 156.0.8078.75, provided by the pinned
Electron 45.2.1 runtime. A private Python service handles JSON/SQLite libraries,
the encrypted password vault, local import and encrypted portable backups.

Application version: **26.11**.
Source: [github.com/mishandreevy/supersearch](https://github.com/mishandreevy/supersearch).
The source link is also available in Settings → About.

## Run

- Linux/macOS: `./run.sh`
- Windows: `run.bat`
- From the project virtual environment: `python main.py`
- Actual engine versions: `python main.py --engine-version`

Python 3.10+ and the platform's Chromium GUI libraries are required. Launch scripts
create `.venv` and install `requirements.txt`. There is one browser implementation:
Electron/Chromium. Qt, PySide6 and a separate Node.js/npm installation are not needed.

The first launch downloads the official Electron platform archive from GitHub.
The source-controlled SHA-256 digest is checked before safe extraction. Versions
and six platform checksums are in `engine/runtime.json`. The installer respects
saved proxy/Tor preferences, does not use ambient proxies and never falls back to
a direct connection. A matching runtime is reused without background update checks.
The runtime cache is `.runtime/`, separate from user data.

Keep Chromium's sandbox enabled. Linux needs unprivileged user namespaces and the
usual Chromium GUI libraries; do not run as root or use `--no-sandbox`.
The real compiled engine versions are checked against the manifest on launch.

## First launch

The welcome page includes the original `assets/logo.svg` and English, Russian,
German, French and Spanish language choices. It asks **Have you used SuperSearch
before?**:

- Returning users can select an encrypted `.supersearch` export, enter its backup
  password and restore their browser data. They can also continue without a file.
- New users can choose a short tour after setup. The tour explains search, tabs,
  privacy, bookmarks, downloads, settings and start-page customization. Back, Next,
  Skip and Escape controls are available. Settings → About → Show me around replays it.

The User Agreement must be accepted before continuing or restoring data. Privacy
Policy and User Agreement remain in English. Preferences preview immediately during
setup, and import from a detected or manually selected Chromium profile is optional.
Search suggestions require an explicit choice and remain disabled in private windows.

## Backup and transfer

Settings → Storage → **Export browser data** writes a password-protected
`.supersearch` file to a location you choose. Use a backup password of at least
12 characters and keep it safely: there is no password recovery.

The portable backup contains saved settings, bookmarks, the still-encrypted password
vault, history, tabs/pins, statistics, start-page shortcuts, wallpaper, cached icons,
filters and installed UserScript/style files. Only normal-window tabs/data and saved
settings are exported. Exported open tabs reopen once after restoration, without
changing the normal keep-tabs preference. The vault keeps its existing master password; the export
password does not unlock the vault.

Website sessions/cookies, cache, extension packages, downloaded files, download
ownership/deletion records and Chromium-internal profiles are not transferred.
Extensions must be installed again. UserScripts/styles restore only after an explicit
opt-in because they can read and modify websites.

Settings → Storage → **Restore browser data** opens the same restore flow used by
the welcome page. It clearly asks to replace current data and restart. The archive's
password, version, paths, hashes, settings, libraries and statistics are validated
before current libraries change. A wrong password or invalid file leaves them intact.
After the old process releases its files, the launcher installs the staged snapshot;
filesystem operation failures roll back the replacement. Existing website sessions
are cleared. Export your current data first if you want to retain it.

The encrypted format uses Fernet authenticated encryption with PBKDF2-HMAC-SHA256,
a random salt and 1,200,000 iterations. Export/restore does not contact a server.
Portable backups are bounded to 128 MB of source data and 180 MB of encrypted file.

## Interface

- Frameless rounded UI, minimize/close, optional expand button and configurable
  maximize/fullscreen behavior. F11 uses true fullscreen; only then do time and
  available laptop battery status appear.
- Persistent pinned tabs, Ctrl/Shift selection and Delete to close a selection.
  Collapsed panels show icons and tooltips. Panels can move to all four edges.
- Horizontal panels are one compact 40 px row with horizontal scrolling. The top
  row also contains window controls. Vertical panel size, UI scale, fonts, colors,
  toolbar order and visibility are customizable. Site colors are not recolored.
- Native isolated Chromium views and split view. Settings, downloads, bookmarks,
  history, password management and the tour stay inside the browser window.
- Customizable start page with the user's original logo, local wallpaper, editable
  shortcuts, local/cached site icons, search and real tracker statistics.
- Optional public IP display uses ipify through the active session's route, caches
  results in memory for ten minutes and never retries through a direct route.
- Rounded optional suggestions, local settings search, contextual password-save
  cards and a custom connection-error screen without certificate bypasses.
- Automatic collision-safe downloads into the system Downloads folder, progress,
  pause/resume/cancel and optional download animation.

Normal settings changes take effect through Apply; Cancel discards the draft.
Startup network/DNS/proxy/Tor, site JavaScript and profile-persistence changes show a
restart marker. Storage cleanup names its categories and asks for confirmation;
reset requires typing `Delete` and returns the active profile to first launch.

## Privacy and local storage

The active profile is `data/electron/`. The first upgrade can copy compatible
libraries from an older `data/` layout without deleting original files. Existing
profiles are never overwritten by that one-time migration. Old Chromium profile
internals are not reused, and there is no second browser launcher.

Normal website storage is in memory by default. Private windows have independent
memory sessions and do not persist history, pins, tracker records or captured
credentials. Downloads are explicit external files and remain after a private window
closes. History recording, public IP display and trusted private UserScripts are
separate preferences.

Tracker filtering supports domain anchors, exceptions and literal substring rules;
it does not claim every EasyList feature. HTTPS upgrades, link cleanup, referrer
removal, third-party cookie heuristics, permission denial and optional API overrides
are configurable. Direct connections still expose their public IP. These features
do not provide Tor Browser's anonymity model or guarantee complete fingerprint protection.

Tor needs an external service on 127.0.0.1:9050 and takes priority over the proxy.
Proxy configuration applies before navigation and has no DIRECT/loopback fallback.
Optional compatible local Manifest V3 extensions require a persistent profile;
Chrome Web Store is not a direct installer. UserScripts/extensions are trusted code.

See [SECURITY.md](SECURITY.md) and [Privacy Policy](docs/PRIVACY.md) for trust boundaries,
network behavior, import limits and deletion scope.

## Development and complete source

The main controller, isolated preloads and HTML/CSS UI are in `engine/`. Shared
Python libraries are in `core/` and `settings/`; public assets are in `assets/`.
Run `python -m unittest discover -s tests -p 'test_*.py' -v` and
`python tests/gui_electron.py`. Tests use temporary profiles and loopback fixtures;
backup tests exercise real process replacement, wrong passwords and rollback.
See [CONTRIBUTING.md](CONTRIBUTING.md) for commands and platform requirements.

`python tools/export_source.py` writes `SOURCE_FULL.md`, `SOURCE_MANIFEST.json` and
`SuperSearch-source.zip`. Every source file is complete. User data, virtual
environments and downloaded runtimes are excluded. Archive CRCs and SHA-256 hashes
are verified. Preserve the notices listed in [THIRD_PARTY.md](THIRD_PARTY.md).

## Source release 26.11

See [CHANGELOG.md](CHANGELOG.md) for release contents and
[docs/RELEASING.md](docs/RELEASING.md) for publication steps. The versioned source
artifact is `SuperSearch-26.11-source.zip`, accompanied by its `.sha256` checksum.
The archive contains the complete source and excludes local browser data.
