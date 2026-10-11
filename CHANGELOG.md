# Changelog

## [1.3.0] 12-10-2026

### Added

- ESP32S3-Weather (Dashboard), the first ESP32-S3 and first non-CYD
  project, on the Waveshare ESP32-S3-Touch-LCD-7.

## [1.2.0] 12-10-2026

### Added

- Filters for project type, screen size and chip, plus a name search. Each
  card shows its type, chip and screen sizes as tags. Choosing a screen size
  also selects that board in each card. A filter group with only one value
  stays hidden.
- `projects.json` entries are now objects: `{ "repo": "…", "type": "Clock" }`.
  A bare repo-name string still works, with the type "Other". The Pages check
  accepts both.

### Changed

- The hub is for all ESP32 projects, not only CYD boards. It is retitled
  **ESP32 Web Installers**, the page notes that native-USB boards need no
  driver, and the README describes the filters and the new `projects.json`
  format.

## [1.1.0] 11-10-2026

### Added

- CYD_Split-Flap-Clock, CYD-RGB-LED-Matrix-HUB75-Retro-Clock,
  CYD_Family_Clock, CYD_BusStop_NSW and TheFlightWall_CYD. The hub now lists
  all 13 CYD installers: the pilot, CYD_AnimatedPixelClock, plus the 12
  projects converted on 09–11-10-2026.
- README: the list of projects on the hub, how the repos fit together, and a
  user guide for keeping the hub current. It covers day-to-day work, releasing
  a version, the rules that keep web install working, when to run the full
  hardware tests, adding a project, shared-tooling updates and
  troubleshooting, with prompts to paste into Claude in VS Code.

## [1.0.1] 10-10-2026

### Fixed

- Each install button loads its manifest with `?v=<version>`, so a manifest
  cached from a project's previous release (Pages: `max-age=600`) is skipped.

### Added

- Projects released since 1.0.0: CYD_WordClock, CYD_Display_Test,
  CYD_WifiScan_Display, AuroraDemo_CYD, CYD_PongClock, F1_CYD_Notifications
  and CYD_TFT_RetroClock.

## [1.0.0] 09-10-2026

### Added

- Hub page `index.html`. It reads `projects.json` and shows each project's
  boards from `/<repo>/index.json`, with ESP Web Tools 10.4.0 install buttons.
  It stores no firmware.
- `projects.json`, listing CYD_AnimatedPixelClock.
- `.github/workflows/pages.yml`, which deploys with GitHub Actions Pages on
  pushes to `main`.
