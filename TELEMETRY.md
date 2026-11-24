# MCPC telemetry

Telemetry is disabled by default in this independent build. The frontend returns `false` from `telemetryEnabled()` unless the build explicitly sets:

```text
VITE_MCPC_TELEMETRY_ENABLED=true
```

The setting is evaluated at build time. The ordinary user preference can opt out when telemetry is enabled, but cannot override a build that has telemetry disabled.

## When an MCPC build enables telemetry

The inherited telemetry client in [src/lib/telemetry.ts](src/lib/telemetry.ts) batches launcher events and sends them to the configured MCPC API:

- `POST /launcher/telemetry` for event batches.
- `POST /launcher/heartbeat` for lobby/game presence.

The API base is configured with `VITE_MCPC_API_BASE_URL`. The Rust core uses `MCPC_API_BASE_URL`.

The inherited event implementation includes device and system information, launcher/game lifecycle, installation and import outcomes, error/crash classifications, screen and UI interaction metadata, search metadata, purchase-flow events, and performance measurements. Review [src/lib/telemetry.ts](src/lib/telemetry.ts), [src/lib/telemetryPrivacy.ts](src/lib/telemetryPrivacy.ts), and [src/lib/crashEvent.ts](src/lib/crashEvent.ts) before enabling it.

## Operator responsibilities

MCPC must publish its own privacy notice before enabling telemetry on distributed builds. That notice should describe the exact fields retained by the MCPC backend, retention periods, access controls, deletion requests, and whether events can be associated with a signed-in account.

No upstream telemetry endpoint or publisher account is configured by default. Do not point this client at another publisher's API.
