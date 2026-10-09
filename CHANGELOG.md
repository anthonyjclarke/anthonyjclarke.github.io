# Changelog

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
