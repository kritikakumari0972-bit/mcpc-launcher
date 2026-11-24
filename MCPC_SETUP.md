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
| VITE_MCPC_UPDATES_ENABLED | Enable own signed update feed | false |

The .example hostname is reserved and is not a working API. It prevents an unconfigured fork from contacting the original publisher. Set MCPC_API_BASE_URL, MCPC_MS_CLIENT_ID, MCPC_DISCORD_APP_ID and MCPC_VOICE_RELAY_DOMAINS in the shell before compiling the Rust core; VITE_MCPC_API_BASE_URL is read by Vite.

## Backend contract

The existing interface expects API routes documented in the source modules, including account/session routes, catalog proxy routes, telemetry, heartbeat, and remote UI/hosting routes. Implement and test those endpoints in an MCPC-owned backend before configuring a real API origin.

Do not point the MCPC client at another publisher's API. The API host must use HTTPS in production. Add the exact origin to the Tauri Content Security Policy in both production and development connect-src lists.

## Microsoft login

Create an MCPC-owned Microsoft Entra application and set MCPC_MS_CLIENT_ID at compile time. The launcher uses Microsoft device-code sign-in and sends the user to Microsoft's own sign-in page; it does not collect the password.

## Party voice

Discord server-join links are disabled until MCPC_JOIN_URL_PREFIX is set to your own HTTPS join-page prefix at compile time. MCPC profile links stay hidden until an MCPC profile base is implemented.

Party voice is disabled until MCPC_VOICE_RELAY_DOMAINS is set at compile time to domains owned and operated by MCPC. The client accepts only those exact domains and their subdomains, and continues to reject local/private IP addresses. Do not add upstream publisher relay domains.

## Updates

The updater plugin has no configured feed or public key in this fork. Both updater checks and fallback manifest checks are disabled by default. Before enabling updates, configure an MCPC-owned HTTPS manifest and keypair, keep the private key in protected CI secrets, and set VITE_MCPC_UPDATES_ENABLED=true only for builds that include the matching public key and endpoint.
