# electron-builder v26 Reference

> Sources:
>
> - <https://www.electron.build/configuration>
> - <https://github.com/electron-userland/electron-builder/blob/master/packages/electron-builder/CHANGELOG.md>
> Fetched: 2026-04-04

## Major Changes in v26

### Breaking Changes (v26.0.0)

- **Build image upgrade**: Base image upgraded from `focal` to `jammy` (Ubuntu 22.04)
- **Python3 requirement**: `python` → `python3` package required
- **Wine-stable**: Updated wine-stable dependency for cross-platform builds
- **TypeScript config support**: `electron-builder.ts` config files now supported
- **Publish-only mode**: New functionality to just publish artifacts without building
- **Corepack support**: Support for `corepack` and `packageManager` field in package.json (v26.3.0)
- **Windows code signing**: Support for latest win-codesign tooling from `electron-builder-binaries` (v26.8.0)

### Key Features Across v26.x

- **Trusted Signing**: Provenance-based releases
- **Apple notarization**: `APPLE_TEAM_ID` env var support; legacy (`altool`) notarization deprecated
- **Packager sub-classes**: Exported from main electron-builder types
- **Better module type support**: Config files with `"type": "module"` properly supported

## Configuration Guide

### Config File Options

electron-builder supports configuration in:

- `electron-builder.yml` / `electron-builder.yaml` (recommended)
- `electron-builder.json` / `electron-builder.json5`
- `electron-builder.js` / `electron-builder.ts`
- `build` key in `package.json`

### Common Configuration (electron-builder.yml)

```yaml
appId: com.company.appname
productName: My App

# Output directory
directories:
  output: build-output

# Application files
files:
  - "out/**/*"
  - "package.json"

# Extra resources (copied to app root, not asar)
extraResources:
  - from: "resources/"
    to: "resources/"

# ASAR archive
asar: true
asarUnpack:
  - "**/*.node"
  - "**/node_modules/better-sqlite3/**"
```

### Windows Configuration

```yaml
win:
  target:
    - target: nsis
      arch: [x64, arm64]
  icon: media/icon.ico
  requestedExecutionLevel: asInvoker
  verifyUpdateCodeSignature: false

nsis:
  oneClick: true
  allowToChangeInstallationDirectory: false
  createDesktopShortcut: true
  createStartMenuShortcut: true
  artifactName: "${productName} Setup ${version}.${ext}"
  deleteAppDataOnUninstall: false
```

### AppX/MSIX Configuration

```yaml
win:
  target:
    - target: appx
      arch: x64

appx:
  identityName: MyAppIdentity
  publisher: CN=PublisherName
  publisherDisplayName: Publisher Display Name
  capabilities:
    - runFullTrust
```

### macOS Configuration

```yaml
mac:
  target:
    - target: dmg
      arch: [x64, arm64]
  icon: media/icon.icns
  category: public.app-category.developer-tools
  hardenedRuntime: true
  entitlements: build/entitlements.mac.plist
  entitlementsInherit: build/entitlements.mac.plist
  notarize: true

dmg:
  sign: false
  contents:
    - x: 130
      y: 220
    - x: 410
      y: 220
      type: link
      path: /Applications
```

### Linux Configuration

```yaml
linux:
  target:
    - target: AppImage
      arch: x64
    - target: deb
      arch: x64
  icon: media/icons
  category: Development
```

### Auto-Update Configuration

```yaml
publish:
  - provider: github
    owner: your-username
    repo: your-repo
```

### Code Signing

#### Windows

Environment variables:

- `WIN_CSC_LINK` — Path to .pfx certificate
- `WIN_CSC_KEY_PASSWORD` — Certificate password
- `CSC_LINK` / `CSC_KEY_PASSWORD` — Generic (used for both platforms)

#### macOS

Environment variables:

- `CSC_LINK` — Base64-encoded .p12 certificate
- `CSC_KEY_PASSWORD` — Certificate password
- `APPLE_ID` — Apple ID email
- `APPLE_APP_SPECIFIC_PASSWORD` — App-specific password
- `APPLE_TEAM_ID` — Team ID (new in v26)

### Native Module Handling

```yaml
# Unpack native modules from asar for runtime loading
asarUnpack:
  - "**/*.node"
  - "**/node_modules/better-sqlite3/**"
  - "**/node_modules/node-powershell/**"
```

### Build Scripts (package.json)

```json
{
  "scripts": {
    "build": "electron-vite build",
    "dist": "electron-vite build && electron-builder --win",
    "dist:mac": "electron-vite build && electron-builder --mac",
    "dist:linux": "electron-vite build && electron-builder --linux",
    "dist:all": "electron-vite build && electron-builder -mwl",
    "postinstall": "electron-builder install-app-deps"
  }
}
```
