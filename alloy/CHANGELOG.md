# Changelog

## 1.3.0 - 2026-10-04

### Fixed
- `level` label was derived from journald PRIORITY, which for container output is
  meaningless (docker tags the whole stderr stream as `err`), so every Home Assistant
  Core line was labeled `error` regardless of its actual level. The level is now parsed
  from the HA log line itself (`YYYY-MM-DD HH:MM:SS.mmm LEVEL (thread) [logger] msg`),
  lowercased, and `WARNING` is normalised to `warn`. Lines without the HA timestamp
  prefix (kernel, journald internals, tracebacks) fall back to `info`.

### Added
- `stage.decolorize` — ANSI colour escape sequences are stripped before parsing and
  are no longer stored in Loki.
- `stage.labelallow` — enforces the approved label set: `job`, `hostname`, `unit`,
  `container_name`, `service_name`, `syslog_identifier`, `transport`, `level`.

## 1.2.1 - 2026-10-04

### Changed
- Moved `build_from` parameters from deprecated `build.yaml` into the Dockerfile (`ARG BUILD_FROM` default)
- `io.hass.url` now points at this fork

## 1.2.0 - 2026-10-04

### Added
- `disable_reporting` option to suppress Alloy's anonymous usage telemetry to `stats.grafana.com` via the `--disable-reporting` CLI flag (resolves #4, credit @matheusfaustino in #8, applied from upstream PR #12)

### Changed
- Telemetry is now disabled by default. Existing installs will stop emitting usage reports on upgrade — set `disable_reporting: false` to opt back in.
- Bundled Grafana Alloy updated from v1.13.1 to v1.20.1

## 1.0.0 - 2026-02-21

### Added
- Initial release
- Grafana Alloy v1.13.1
- Systemd journal log shipping to Loki
- Journal field relabeling (unit, hostname, syslog_identifier, transport, container_name, level)
- Debug UI on port 12345
- Configurable Loki URL, log level, and additional config
- Watchdog health check via Alloy's `/-/ready` endpoint
- Support for amd64 and aarch64 architectures
