# Changelog

## 1.3.3 - 2026-10-05

### Added
- `stage.match` + `stage.drop` for the mosquitto ACL-check noise line
  (`received null username, clientid or topic, or access is equal or less than 0
  for acl check`): an identical cosmetic broker-internal error repeated
  ~12/s (~93% of total journal volume). All matching lines are dropped
  (Alloy `stage.drop` has no 1-in-N sampling); the drop count is visible in
  `loki_process_dropped_lines_total{reason="mosquitto_acl_noise"}` on the
  debug UI metrics endpoint. Connections, auth, keepalive and disconnect
  lines are untouched.

## 1.3.2 - 2026-10-04

### Fixed
- `WARNING` lines were labeled `warning`, not `warn`: `stage.replace` with a
  `source` argument does not rewrite the extracted value in Alloy 1.20.x
  (verified empirically). Normalisation moved into the `stage.template` step.
  Verified in a sandbox: `debug`, `info`, `warn`, `error`, `critical` all produced correctly.

## 1.3.1 - 2026-10-04

### Fixed
- 1.3.0 used the promtail-era `stage.labelallow`, which does not exist in Alloy and
  prevented the pipeline from starting. Replaced with Alloy's `stage.label_keep`
  (same semantics).

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
