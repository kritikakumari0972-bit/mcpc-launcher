# MCPC service setup

This fork is deliberately separated from the original launcher's infrastructure.

## Environment variables

| Variable | Purpose | Default |
| --- | --- | --- |
| MCPC_API_BASE_URL | Rust backend root | https://api.mcpc.example/v2 |
| VITE_MCPC_API_BASE_URL | Frontend backend root | https://api.mcpc.example/v2 |
| MCPC_MS_CLIENT_ID | MCPC Microsoft Entra application ID | Not configured |
| MCPC_DISCORD_APP_ID | MCPC Discord Rich Presence application ID | Not configured |
| MCPC_VOICE_RELAY_DOMAINS | Comma-separated MCPC-owned voice relay domains | Empty (voice disabled) |
| MCPC_JOIN_URL_PREFIX | MCPC-owned HTTPS join-page URL prefix | Empty (join button disabled) |
| MCPC_MOD_MANIFEST_URL | MCPC signed cosmetics-mod manifest | .example (disabled) |
| MCPC_MOD_BETA_MANIFEST_URL | MCPC beta mod manifest | .example (disabled) |
| MCPC_MOD_BLOCKED_URL | MCPC mod kill-switch endpoint | .example (disabled) |
| MCPC_MOD_BASE_URL | MCPC mod artifact base URL | .example (disabled) |
| MCPC_MOD_MANIFEST_PUBKEY | MCPC minisign public key (base64 text wrapper) | Empty (mod installs fail closed) |
| MCPC_TEXTURE_HOSTS | Exact MCPC skin/texture hostnames, comma-separated | Empty |
| MCPC_TEXTURE_HOST_SUFFIXES | MCPC-owned skin/texture DNS suffixes, comma-separated | Empty |
| MCPC_CATALOG_DOWNLOAD_HOSTS | Exact MCPC catalog file hosts, comma-separated | .example hosts only |
| MCPC_PACK_HOST | Exact modpack archive host | packs.mcpc.example |
| MCPC_PACK_ICON_HOST | Exact modpack icon host | cdn.mcpc.example |
| VITE_MCPC_UPDATES_ENABLED | Enable own signed update feed | false |
| VITE_MCPC_REMOTE_UI_ENABLED | Enable remote signed MCPC screens | false |
| VITE_MCPC_REMOTE_UI_BASE_URL | MCPC remote UI bundle root | https://ui.mcpc.example/launcher-ui |
| VITE_MCPC_REMOTE_UI_SITE | Allowed origin for MCPC remote UI | https://mcpc.example |
| VITE_MCPC_REMOTE_UI_PUBLIC_KEY | MCPC Ed25519 public key (base64) | Empty (remote UI disabled) |
| VITE_MCPC_TELEMETRY_ENABLED | Enable optional usage telemetry | false |

The .example hostname is reserved and is not a working API. It prevents an unconfigured fork from contacting the original publisher. Set MCPC_API_BASE_URL, MCPC_MS_CLIENT_ID, MCPC_DISCORD_APP_ID and MCPC_VOICE_RELAY_DOMAINS in the shell before compiling the Rust core; VITE_MCPC_API_BASE_URL is read by Vite.

## Backend contract

The existing interface expects API routes documented in the source modules, including account/session routes, catalog proxy routes, telemetry, heartbeat, and remote UI/hosting routes. Implement and test those endpoints in an MCPC-owned backend before configuring a real API origin.

Do not point the MCPC client at another publisher's API. The API host must use HTTPS in production. Add the exact origin to the Tauri Content Security Policy in both production and development connect-src lists.

## Microsoft login

Create an MCPC-owned Microsoft Entra application and set MCPC_MS_CLIENT_ID at compile time. The launcher uses Microsoft device-code sign-in and sends the user to Microsoft's own sign-in page; it does not collect the password.

## Optional cosmetics mod

The upstream publisher's mod feed, CDN, signing key and kill-switch URL are not used. The mod installer fails closed until all MCPC-owned manifest/CDN/kill-switch URLs and a matching minisign public key are supplied at compile time. The default .example hosts are reserved and intentionally unavailable.

## Catalog download hosts

MCPC catalog files and modpacks are accepted only from exact HTTPS hostnames. Configure MCPC_CATALOG_DOWNLOAD_HOSTS, MCPC_PACK_HOST and MCPC_PACK_ICON_HOST to your own storage hosts before enabling the catalog. The `.example` defaults are reserved placeholders. Public Modrinth and CurseForge CDN hosts remain separately allowlisted where the relevant integration requires them.

## Party voice

Discord server-join links are disabled until MCPC_JOIN_URL_PREFIX is set to your own HTTPS join-page prefix at compile time. MCPC profile links stay hidden until an MCPC profile base is implemented.

Party voice is disabled until MCPC_VOICE_RELAY_DOMAINS is set at compile time to domains owned and operated by MCPC. The client accepts only those exact domains and their subdomains, and continues to reject local/private IP addresses. Do not add upstream publisher relay domains.

## Telemetry

Usage telemetry and heartbeat uploads are disabled by default for this independent build. Enable VITE_MCPC_TELEMETRY_ENABLED=true only after the MCPC API endpoint, privacy notice and opt-out behavior are in place.

## Remote website UI

Remote screens remain disabled until MCPC publishes its own bundle, creates its own Ed25519 signing keypair, and sets the base URL, site origin and 32-byte public key. Set VITE_MCPC_REMOTE_UI_ENABLED=true only after those values are configured. Never use the original publisher's public key or remote site.

## Updates

The updater plugin has no configured feed or public key in this fork. Both updater checks and fallback manifest checks are disabled by default. Before enabling updates, configure an MCPC-owned HTTPS manifest and keypair, keep the private key in protected CI secrets, and set VITE_MCPC_UPDATES_ENABLED=true only for builds that include the matching public key and endpoint.
