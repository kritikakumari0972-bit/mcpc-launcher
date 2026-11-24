# MCPC service setup

This fork is deliberately separated from the original launcher's infrastructure.

## Environment variables

| Variable | Purpose | Default |
| --- | --- | --- |
| MCPC_API_BASE_URL | Rust backend root | https://api.mcpc.example/v2 |
| VITE_MCPC_API_BASE_URL | Frontend backend root | https://api.mcpc.example/v2 |
| MCPC_MS_CLIENT_ID | MCPC Microsoft Entra application ID | Not configured |
| MCPC_DISCORD_APP_ID | MCPC Discord Rich Presence application ID | Not configured |
| VITE_MCPC_UPDATES_ENABLED | Enable own signed update feed | false |

The .example hostname is reserved and is not a working API. It prevents an unconfigured fork from contacting the original publisher.

## Backend contract

The existing interface expects API routes documented in the source modules, including account/session routes, catalog proxy routes, telemetry, heartbeat, and remote UI/hosting routes. Implement and test those endpoints in an MCPC-owned backend before configuring a real API origin.

Do not point the MCPC client at another publisher's API. The API host must use HTTPS in production. Add the exact origin to the Tauri Content Security Policy in both production and development connect-src lists.

## Microsoft login

Create an MCPC-owned Microsoft Entra application and set MCPC_MS_CLIENT_ID at compile time. The launcher uses Microsoft device-code sign-in and sends the user to Microsoft's own sign-in page; it does not collect the password.

## Updates

The updater plugin has no configured feed or public key in this fork. Both updater checks and fallback manifest checks are disabled by default. Before enabling updates, configure an MCPC-owned HTTPS manifest and keypair, keep the private key in protected CI secrets, and set VITE_MCPC_UPDATES_ENABLED=true only for builds that include the matching public key and endpoint.
