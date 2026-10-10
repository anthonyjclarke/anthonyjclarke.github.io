# anthonyjclarke.github.io

Browser installers for my ESP32 CYD (Cheap Yellow Display) projects:
<https://anthonyjclarke.github.io/>. Pick a project and a board, plug the board
in with a USB data cable, and click **Connect & install** in desktop Chrome,
Edge or Opera. You don't need PlatformIO or esptool. A board that already runs
the firmware is offered **Update**, which keeps its settings and WiFi.

This README is also my user guide for keeping the hub current as projects
change.

---

## Projects on the hub

| Project                              | What it is                         | Boards           |
|:-------------------------------------|:-----------------------------------|:-----------------|
| CYD_AnimatedPixelClock               | Animated retro-arcade pixel clock  | 2.8″, 2.4″, 4.0″ |
| CYD_WordClock                        | Word clock                         | 2.8″             |
| CYD_Display_Test                     | Display test; new-project scaffold | 2.8″, 4.0″       |
| CYD_WifiScan_Display                 | WiFi network scanner               | 2.8″             |
| AuroraDemo_CYD                       | Aurora matrix demos (no WiFi)      | 2.8″, 4.0″       |
| CYD_PongClock                        | Pong clock                         | 2.8″             |
| F1_CYD_Notifications                 | F1 sessions and Telegram alerts    | 2.8″             |
| CYD_TFT_RetroClock                   | MAX7219 LED matrix clock look      | 2.8″             |
| CYD_Split-Flap-Clock                 | Split-flap clock                   | 2.8″             |
| CYD-RGB-LED-Matrix-HUB75-Retro-Clock | HUB75 LED matrix clock look        | 2.8″             |
| CYD_Family_Clock                     | Family and world clock             | 2.8″             |
| CYD_BusStop_NSW                      | NSW bus departures (own API key)   | 2.8″             |
| TheFlightWall_CYD                    | Flights overhead (own API keys)    | 2.8″, 4.0″       |

Each project's installer is at `https://anthonyjclarke.github.io/<repo>/`, and
its source at `https://github.com/anthonyjclarke/<repo>`. Versions aren't
listed here on purpose: the hub reads each project's live `index.json`, so it
always shows the latest release. The boards are the 2.8″ ESP32-2432S028R, the
2.4″ ESP32-2432S024 and the 4.0″ ESP32-32E.

Not on the hub: ESP32_Touchdown_Retro_Clock and the ESP32-S3 projects. The hub
is for CYD boards only.

---

## How it works

Each project builds and publishes itself. Its `.github/workflows/firmware.yml`
calls the shared, reusable workflow in
[cyd-web-installer](https://github.com/anthonyjclarke/cyd-web-installer). On a
`v*` tag, that workflow builds every board, publishes a GitHub release, and
deploys the project's own installer page to `anthonyjclarke.github.io/<repo>/`.

This repo holds only the hub page (`index.html`) and the list of projects
(`projects.json`). The project pages share the hub's origin, so the hub's
buttons point straight at each project's manifests. It stores no firmware and
never needs rebuilding when a project releases.

| Repo                       | Visibility | Holds                                |
|:---------------------------|:-----------|:-------------------------------------|
| `anthonyjclarke.github.io` | Public     | Hub page and project list            |
| `cyd-web-installer`        | Public     | Shared workflow, tools, copy-ins     |
| `cyd-web-installer-notes`  | Private    | Inventory, plan, per-project runbook |
| Each project repo          | Public     | Firmware, `firmware.yml`, its docs   |

---

## User guide – keeping the hub current

### The one rule

**A new version reaches the hub only through a `v*` tag on `main`.** Pushing
to `dev` or `main` builds and checks the firmware, but publishes nothing. The
hub picks up a new release on its next page load; you don't edit this repo for
a new version.

### Day to day: changing a project

Work in VS Code with the project folder open. Claude Code does the edits, and
the terminal panel runs the commands.

1. **Work on `dev`.** `FIRMWARE_VERSION` in `include/config.h` carries a
   `-dev` suffix there, for example `1.4.0-dev`.
2. **Push `dev` whenever you like.** CI builds every board and keeps a
   `site-preview` artifact for 7 days. Download it to try the installer
   locally before a release. It's built without your `secrets.h`, so it's what
   users will get:

   ```bash
   gh run list --branch dev --limit 1
   ```

   ```bash
   gh run download <run-id> --name site-preview --dir ../site-preview
   ```

   ```bash
   python3 -m http.server -d ../site-preview 8000
   ```

   Then open `http://localhost:8000` in Chrome.
3. **Keep the web-install rules** below. Each project's `CLAUDE.md` has a
   *Web installer and releases* section, so Claude sees them in every session.
4. **Note owed tests.** If your change hits a full-test trigger (see below),
   add it to *Tests owed* in the project's `docs/WEB_INSTALLER.md`.

To start a session, paste:

```text
Work on <repo> on dev. Read CLAUDE.md first, including its "Web installer
and releases" rules. Commit locally with type(scope): description and
update CHANGELOG.md; ask me before any push.
```

### Releasing a new version

Do this when `dev` is ready. Replace `X.Y.Z` with the new version.

1. **On `dev`:**
   - Set `FIRMWARE_VERSION` to `X.Y.Z`, without the suffix.
   - Date the CHANGELOG entry `## [X.Y.Z] DD-MM-YYYY`.
   - Update the README version badge, if the project has one.
   - Commit `docs(release): cut vX.Y.Z` and push `dev`.
2. **Clear *Tests owed*** in `docs/WEB_INSTALLER.md` if any are listed (see
   "When to run the full hardware tests").
3. **Merge to `main` and push it.** Wait for `main`'s CI run to pass.

   ```bash
   git checkout main && git merge dev && git push origin main
   ```

4. **Tag and push the tag.** This is the step that publishes.

   ```bash
   git tag vX.Y.Z && git push origin vX.Y.Z
   ```

5. **Check it.**
   - `gh run watch` should show build and publish both passing.
   - `https://anthonyjclarke.github.io/<repo>/` shows `X.Y.Z`.
   - The GitHub release has `*-firmware.bin`, `*-merged.bin` and
     `SHA256SUMS.txt`.
   - The hub card shows the new version.
6. **Reopen `dev`.** Set the next version (`X.Y+1.0-dev`), commit
   `chore(release): open X.Y+1.0-dev on dev`, and push.

The workflow refuses to publish when any of these is true:

- the tag isn't exactly `v` + `FIRMWARE_VERSION`
- the version has a suffix such as `-dev`
- the tagged commit isn't on `main`
- `include/secrets.h` is committed

To have Claude do steps 1–3 and 5–6, paste this and push the tag yourself at
step 4:

```text
Release <repo> as vX.Y.Z following the "Releasing a new version" steps in
anthonyjclarke.github.io/README.md. Ask me before each push and before the
merge to main. Don't push the tag – tell me the exact command and wait.
After I've pushed it, check the release run, the live page and the release
assets, then reopen dev.
```

### Rules that keep web install working

| Never                                 | Because                                 |
|:--------------------------------------|:----------------------------------------|
| Change `PROJECT_NAME`                 | Update turns into Install + erase       |
| Change the partition table quietly    | Needs an erase; say so in release notes |
| Publish a local build                 | It contains your `secrets.h` WiFi       |
| Put the merged image in a manifest    | It wipes NVS: WiFi and settings         |
| Add Improv back to `lib_deps`         | The vendored copy has two fixes         |
| Block `loop()` for over ~1 s          | Improv must answer within 1.5 s         |
| Drop `tools/merge_bin.py` from builds | The manifest needs `flash_parts.json`   |
| Unpin `platform =`                    | Unpinned resolves to a 3.x core         |
| Store settings in a shipped FS image  | Every Update rewrites that partition    |

If a project's `loop()` has to block (long fetches or animations), it runs
`improvTick()` from a small FreeRTOS task instead, as Pong and FlightWall do.

### When to run the full hardware tests

Going live needs only a smoke test on one board:

1. Erase it and install.
2. **Configure WiFi**.
3. Click **Connect** again and check it says "Connected to …" with the project
   name and version.

Run the full set (fresh install, Update keeps settings, Update from `app1`,
wrong board) on each board when any of these change:

- the partition table or the platform version
- WiFi, Improv or `loop()` timing code
- a board env, added or changed
- the NVS layout or keys
- the first release with any OTA

Record the results, with the date and board MAC, in `docs/WEB_INSTALLER.md`.

ESP Web Tools shows **Update** only when the board's version differs from the
installer's. To test Update, flash the board from PlatformIO with a temporary
version such as `X.Y.Z-dev.0`, and never commit that change.

### Adding a new project

1. **Start installer-ready.** Copy CYD_Display_Test, the scaffold, or convert
   an existing repo with `RUNBOOK.md` in the private `cyd-web-installer-notes`
   repo. Its last section is a prompt to paste into Claude.
2. **One-time GitHub setup** for the new repo. Without the tag rule, a release
   builds and then the deploy is refused:

   ```bash
   gh api -X POST repos/anthonyjclarke/<repo>/pages -f build_type=workflow
   ```

   ```bash
   gh api -X POST repos/anthonyjclarke/<repo>/environments/github-pages/deployment-branch-policies -f name='v*' -f type=tag
   ```

   If the second command says the environment doesn't exist, run the
   environment step in `RUNBOOK.md` step 4 first.
3. **Release it** as above.
4. **List it on the hub.** Add the repo name to `projects.json` in this repo,
   in display order, then commit and push to `main`.
   `.github/workflows/pages.yml` checks the list and deploys within a minute.
   A name listed before its first release shows "No installer published yet"
   instead of breaking the page.

To remove a project, delete its name from `projects.json` and push. Don't
rename a project repo: its installer URL changes with it.

### When the shared tooling changes

Every project calls `cyd-web-installer` at `@main`, so a tooling change
reaches each project at its **next release**. Live installers don't change
until then. Projects keep their own copies of `tools/merge_bin.py`,
`lib/ImprovWiFi/` and `src/network/improv_setup.*`, so read the
cyd-web-installer `CHANGELOG.md`. When an entry says "copy again", do that on
`dev` before the next release.

### Troubleshooting

| Symptom                            | Fix                                      |
|:-----------------------------------|:-----------------------------------------|
| Tag run: version doesn't match     | Fix the version on `main`, re-tag        |
| Tag run: "is not on main"          | Merge `dev` into `main` first, re-tag    |
| Deploy refused for `github-pages`  | Add the `v*` tag rule (one-time setup)   |
| Offers Install instead of Update   | Name mismatch, slow Improv, or old lib   |
| No Install or Update button        | Same version already on the board        |
| No serial port in Chrome           | Close other monitors; data cable; driver |
| Card says "No installer published" | Not released yet, or a typo in the list  |

To re-tag, delete the failed tag (`git tag -d vX.Y.Z` and
`git push origin :refs/tags/vX.Y.Z`), fix `main`, then tag again.

Chrome keeps the serial port while the installer dialog is open. Close the
dialog before using a serial monitor or `pio run -t upload`.

---

## Try the hub locally

The page fetches `/<repo>/index.json` from its own origin. To test it, put a
project's live `index.json` and manifests in a folder named after the repo,
next to `index.html`, then serve the folder:

```bash
python3 -m http.server 8000
```

One-time setup for this repo: **Settings → Pages → Source** = GitHub Actions.
