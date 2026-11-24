# Build MCPC Launcher

This guide builds the current MCPC Launcher source checkout. The first build downloads Rust and frontend dependencies and may take a while.

## 1. Prerequisites

### Windows 10/11

Open PowerShell:

    winget install --id Git.Git -e
    winget install --id Oven-sh.Bun -e
    winget install --id Rustlang.Rustup -e
    winget install --id Microsoft.VisualStudio.2022.BuildTools -e --override "--quiet --wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended"

Restart PowerShell after installation. Windows needs the Microsoft C++ build tools and WebView2 runtime.

### macOS

    xcode-select --install
    curl -fsSL https://bun.sh/install | bash
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

### Debian/Ubuntu Linux

    sudo apt-get install -y git curl libwebkit2gtk-4.1-dev libappindicator3-dev librsvg2-dev patchelf libxdo-dev libssl-dev build-essential file wget
    curl -fsSL https://bun.sh/install | bash
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

Other distributions need the equivalent Tauri system packages.

## 2. Get the source

    git clone https://github.com/kritikakumari0972-bit/mcpc-launcher.git
    cd mcpc-launcher
    bun install

## 3. Configure your own services

Do not use credentials, application IDs, API domains or update-signing keys belonging to another launcher.

### Microsoft sign-in

Register an application for MCPC in Microsoft Entra ID and set its application (client) ID before compiling the Rust core.

PowerShell:

    $env:MCPC_MS_CLIENT_ID = "YOUR_MCPC_MICROSOFT_APPLICATION_ID"

macOS/Linux:

    export MCPC_MS_CLIENT_ID="YOUR_MCPC_MICROSOFT_APPLICATION_ID"

Without this value, Microsoft sign-in returns a clear configuration error instead of using another publisher's ID.

### MCPC API

The backend is a separate component and is not included yet. Set both variables to your own HTTPS API root ending in /v2:

    export MCPC_API_BASE_URL="https://api.your-domain.example/v2"
    export VITE_MCPC_API_BASE_URL="https://api.your-domain.example/v2"

For Vite browser development, these values can also be placed in .env.local. The Tauri CSP in src-tauri/tauri.conf.json must explicitly allow your API origin under both production and development connect-src lists.

The default is https://api.mcpc.example/v2, a reserved example address. Backend-dependent account, social, proxy and website features remain unavailable until your backend implements their API contracts.

### Discord Rich Presence (optional)

Set MCPC_DISCORD_APP_ID to an application ID created for MCPC. Without it, Rich Presence remains inactive.

### Automatic updates

Automatic update checks are intentionally disabled. Enable them only after you have:
1. Published an MCPC update manifest and platform artifacts.
2. Generated an MCPC-only signing keypair.
3. Kept the private key outside the repository and stored it as a protected release secret.
4. Added the matching public key and HTTPS endpoint to src-tauri/tauri.conf.json.

Do not copy another publisher's key or endpoint. VITE_MCPC_UPDATES_ENABLED is off by default.

## 4. Run in development

    bun run build:web
    bun run tauri dev

For the browser-only Vite preview:

    bun run dev

## 5. Build installers

    bun run tauri build

Expected output folders:

| Platform | Output |
| --- | --- |
| Windows | src-tauri/target/release/bundle/nsis/ |
| macOS | src-tauri/target/release/bundle/dmg/ and bundle/macos/ |
| Linux | src-tauri/target/release/bundle/deb/, rpm/ and appimage/ |

Build artifacts are unsigned unless you configure your own platform signing certificates. Do not describe an unsigned build as publisher-verified.

## 6. Checks

    bun run typecheck
    bun run test
    bun run build:web:release

The full Rust test suite can be run from src-tauri with cargo test --workspace.
