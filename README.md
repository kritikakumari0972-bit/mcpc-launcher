# MCPC Launcher

An independent, cross-platform Minecraft Java launcher built with **Tauri 2, Rust, React and TypeScript**.

MCPC Launcher is being developed as its own product. This repository is currently in the independence and rebranding phase; do not treat current builds as a finished public release yet.

## Current codebase

The existing launcher foundation includes:

- Minecraft Java version metadata, game downloads and launch pipeline.
- Automatic Java runtime selection and installation.
- Fabric, Forge, Quilt and NeoForge installation.
- Separate game instances, instance import/export and modpack support.
- Modrinth and CurseForge content integration.
- World management, backups, screenshots and server lists.
- JVM and graphics tuning helpers.
- Microsoft account sign-in and Minecraft profile/skin operations, once an MCPC-owned Microsoft app registration is configured.

Some social, account, catalog-proxy and website features depend on an MCPC backend. That backend is not included in this repository yet. The default API address is a reserved .example domain, so an unconfigured build does not send requests to the original publisher's servers.

## Independence and configuration

- **Microsoft sign-in:** disabled until the build supplies its own MCPC_MS_CLIENT_ID.
- **Backend:** configure MCPC_API_BASE_URL for the Rust core and VITE_MCPC_API_BASE_URL for the frontend. Both must point to your own HTTPS API ending in /v2. See MCPC_SETUP.md.
- **Discord Rich Presence:** optional; it uses MCPC_DISCORD_APP_ID and is inactive when no ID is configured.
- **Automatic updates:** disabled until MCPC has its own release feed and signing key. Never reuse another publisher's updater key or endpoints.
- **Deep links:** the application uses the mcpc:// scheme.

## Build

See BUILDING.md for prerequisites and platform-specific instructions.

The project uses Bun for the frontend and Cargo for the Tauri/Rust core:

    bun install
    bun run build:web
    bun run tauri dev

To create a release bundle after installing the platform prerequisites:

    bun run tauri build

## Project structure

- src/ — React and TypeScript user interface.
- src-tauri/src/engine/ — Rust game, profile, content and account engines.
- src-tauri/src/commands/ — validated Tauri IPC commands.
- src-tauri/tauri.conf.json — product identity, CSP and bundle configuration.

## License and provenance

MCPC Launcher is an independent fork based on the Millida Launcher codebase, which is distributed under **GPL-3.0-only**. The original project name, marks, and artwork are not licensed by the GPL. This project is not affiliated with or endorsed by Millida, Mojang or Microsoft.

Keep the applicable license and third-party notices when redistributing source. Replace any remaining upstream artwork before publishing installers. Minecraft is a trademark of Mojang Studios.
