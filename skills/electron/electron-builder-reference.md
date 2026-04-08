# electron-builder 26+ Reference Guide

Detailed reference for electron-builder 26+ development. Covers configuration, targets, code signing, auto-updates, and common patterns.

---

## Configuration

### Config File Formats

electron-builder supports multiple config file formats (in priority order):

1. `electron-builder.yml` / `electron-builder.yaml` (recommended)
2. `electron-builder.json` / `electron-builder.json5`
3. `electron-builder.js` / `electron-builder.ts` (new in v26)
4. `build` key in `package.json`

### Minimal Configuration

```yaml
# electron-builder.yml
appId: com.company.appname
productName: My App

directories:
  output: build-output

win:
  target: nsis
```

### Complete Configuration Example

```yaml
# electron-builder.yml
appId: com.company.appname
productName: My App

# Output directory
directories:
  output: build-output

# Files to include in the app package
files:
  - "out/**/*"
  - "package.json"
  - "!**/*.{ts,tsx,map}"
  - "!node_modules/.cache"

# Extra resources — copied outside asar
extraResources:
  - from: "resources/"
    to: "resources/"
    filter:
      - "**/*"

# ASAR archive settings
asar: true
asarUnpack:
  - "**/*.node"
  - "**/node_modules/better-sqlite3/**"

# Windows configuration
win:
  target:
    - target: nsis
      arch: [x64, arm64]
  icon: media/icon.ico
  requestedExecutionLevel: asInvoker
  verifyUpdateCodeSignature: false

# NSIS installer configuration
nsis:
  oneClick: true
  allowToChangeInstallationDirectory: false
  createDesktopShortcut: true
  createStartMenuShortcut: true
  artifactName: "${productName} Setup ${version}.${ext}"
  deleteAppDataOnUninstall: false
  perMachine: false

# Auto-update publishing
publish:
  - provider: github
    owner: your-username
    repo: your-repo
```

---

## Build Targets

### Windows Targets

| Target | Description | Use Case |
| -------- | ------------- | ---------- |
| `nsis` | NSIS installer (.exe) | Standard desktop install, auto-updates |
| `nsis-web` | NSIS web installer | Large apps, download during install |
| `appx` / `msix` | Windows Store package | Store distribution, sandboxed |
| `portable` | Portable executable | No installation needed |
| `msi` | Windows Installer (.msi) | Enterprise deployment |
| `msiWrapped` | MSI wrapping NSIS | Enterprise + custom install |
| `squirrel` | Squirrel installer | Legacy auto-update support |

### macOS Targets

| Target | Description |
| -------- | ------------- |
| `dmg` | Disk image |
| `pkg` | macOS package installer |
| `mas` | Mac App Store |
| `zip` | Zip archive (for Sparkle updates) |

### Linux Targets

| Target | Description |
| -------- | ------------- |
| `AppImage` | Universal Linux portable |
| `deb` | Debian package |
| `rpm` | Red Hat package |
| `snap` | Snap package |
| `flatpak` | Flatpak package |

---

## NSIS Configuration (Detailed)

```yaml
nsis:
  # Installer type
  oneClick: true                              # One-click install (no wizard)
  allowToChangeInstallationDirectory: false    # Only if oneClick=false

  # Shortcuts
  createDesktopShortcut: true                  # or "always" for reinstall
  createStartMenuShortcut: true
  shortcutName: "My App"

  # Naming
  artifactName: "${productName} Setup ${version}.${ext}"
  installerHeaderIcon: "media/icon.ico"

  # Behavior
  perMachine: false                            # true = requires admin
  allowElevation: true                         # Allow UAC elevation
  deleteAppDataOnUninstall: false
  runAfterFinish: true

  # Custom NSIS scripting
  include: "build/installer.nsh"               # Custom NSIS script
  script: "build/installer.nsi"                # Full custom NSIS script

  # Uninstaller
  uninstallDisplayName: "${productName}"
```

### Custom NSIS Script (installer.nsh)

```nsis
!macro preInit
  ; Runs before installer starts
  ; Check prerequisites, etc.
!macroend

!macro customInstall
  ; After installation completes
  ; Add registry entries, env vars, etc.
  WriteRegStr HKCU "Software\MyApp" "InstallPath" "$INSTDIR"
!macroend

!macro customUnInstall
  ; During uninstallation
  DeleteRegKey HKCU "Software\MyApp"
!macroend
```

---

## AppX/MSIX Configuration

```yaml
win:
  target:
    - target: appx
      arch: x64

appx:
  identityName: MyAppIdentity                  # Package identity name
  publisher: CN=PublisherName                  # Must match certificate
  publisherDisplayName: Publisher Display Name
  applicationId: MyApp
  
  # Capabilities
  capabilities:
    - runFullTrust                              # Full trust (desktop bridge)
    - internetClient
  
  # Display name (defaults to productName)
  displayName: My App
  
  # Optional: custom manifest
  # manifestTemplate: build/AppxManifest.xml
```

---

## Code Signing

### Windows Code Signing

#### Environment Variables

```bash
# PFX certificate
WIN_CSC_LINK=path/to/certificate.pfx
WIN_CSC_KEY_PASSWORD=password

# EV certificate (USB token)
# Use signtool integration or custom sign function
```

#### Configuration

```yaml
win:
  certificateFile: certificate.pfx
  certificatePassword: ${env.WIN_CSC_KEY_PASSWORD}
  
  # Skip signing for development
  # signingHashAlgorithms: [sha256]
  
  # For self-signed (dev only)
  verifyUpdateCodeSignature: false
```

### macOS Code Signing & Notarization

#### Environment Variables (v26+)

```bash
CSC_LINK=base64-encoded-p12-cert
CSC_KEY_PASSWORD=cert-password
APPLE_ID=your@apple.com
APPLE_APP_SPECIFIC_PASSWORD=xxxx-xxxx-xxxx-xxxx
APPLE_TEAM_ID=XXXXXXXXXX               # New in v26
```

#### Configuration

```yaml
mac:
  hardenedRuntime: true
  gatekeeperAssess: false
  entitlements: build/entitlements.mac.plist
  entitlementsInherit: build/entitlements.mac.plist
  notarize: true                        # Simplified in v26
```

---

## Auto-Update Integration

### Publish Configuration

```yaml
# GitHub Releases
publish:
  - provider: github
    owner: your-username
    repo: your-repo
    releaseType: release              # or "draft" or "prerelease"

# S3 / Generic Server
publish:
  - provider: generic
    url: https://your-update-server.com/releases
```

### electron-updater Setup

```ts
// main/updater.ts
import { autoUpdater } from 'electron-updater'
import { app, BrowserWindow } from 'electron'

export function initAutoUpdater(win: BrowserWindow) {
  autoUpdater.autoDownload = false
  autoUpdater.autoInstallOnAppQuit = true

  autoUpdater.on('update-available', (info) => {
    win.webContents.send('update-available', info)
  })

  autoUpdater.on('update-downloaded', (info) => {
    win.webContents.send('update-downloaded', info)
  })

  autoUpdater.on('error', (error) => {
    win.webContents.send('update-error', error.message)
  })

  // Check for updates after app ready
  autoUpdater.checkForUpdates()
}
```

---

## Native Module Handling

### Better-SQLite3, node-powershell, etc

```yaml
# Unpack native .node binaries from asar
asarUnpack:
  - "**/*.node"
  - "**/node_modules/better-sqlite3/**"
  - "**/node_modules/node-powershell/**"
```

### Install app dependencies separately

```json
{
  "scripts": {
    "postinstall": "electron-builder install-app-deps"
  }
}
```

### Rebuild native modules

```bash
# Force rebuild for current Electron version
npx electron-rebuild
```

---

## v26 Major Changes Summary

| Version | Change | Impact |
| --------- | -------- | -------- |
| v26.0.0 | Base image: `focal` → `jammy` | Docker/CI builds need update |
| v26.0.0 | `python` → `python3` required | Build environment update |
| v26.0.0 | TypeScript config files (`.ts`) | New config format option |
| v26.0.0 | `electron-rebuild` for native deps | Replaces previous rebuild mechanism |
| v26.3.0 | Corepack & `packageManager` support | Package manager detection improved |
| v26.8.0 | Latest win-codesign artifacts | Windows signing updated |
| v26.x | Legacy `altool` notarization deprecated | Use `notarytool` (automatic) |
| v26.x | `APPLE_TEAM_ID` env var support | Required for Apple ID notarization |

---

## Build Scripts Reference

```json
{
  "scripts": {
    "build": "electron-vite build",
    "dist": "electron-vite build && electron-builder --win",
    "dist:mac": "electron-vite build && electron-builder --mac",
    "dist:linux": "electron-vite build && electron-builder --linux",
    "dist:all": "electron-vite build && electron-builder -mwl",
    "dist:portable": "electron-vite build && electron-builder --win portable",
    "publish": "electron-vite build && electron-builder --win --publish always",
    "postinstall": "electron-builder install-app-deps"
  }
}
```

---

## Common Mistakes

| Mistake | Fix |
| --------- | ----- |
| Changing `appId` after release | Never change — breaks updates and uninstall |
| Forgetting `asarUnpack` for `.node` files | Native modules fail to load from asar |
| Missing `postinstall` script | Native modules not rebuilt for Electron |
| Not unpacking `better-sqlite3` | Database crashes at runtime |
| Using `certFile` with wrong path in CI | Use environment variables (`WIN_CSC_LINK`) |
| Missing `python3` in Docker build | Update build image to `jammy` or install `python3` |
| Using deprecated `altool` notarization | Remove `altool` config — `notarytool` is automatic |
| Setting `requestedExecutionLevel: requireAdministrator` | Use `asInvoker` + on-demand elevation instead |

---

## Debugging Build Issues

```bash
# Verbose logging
DEBUG=electron-builder electron-builder --win

# Check output structure
ls -la build-output/

# Verify asar contents
npx asar list build-output/win-unpacked/resources/app.asar

# Test portable build (faster iteration)
electron-builder --win portable
```
