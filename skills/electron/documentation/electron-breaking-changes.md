# Electron Breaking Changes (v36–v41+)

> Source: <https://www.electronjs.org/docs/latest/breaking-changes>
> Fetched: 2026-04-04

## Convention

- **API Changed**: Code that hasn't been updated is guaranteed to throw.
- **Behavior Changed**: Behavior changed, may not throw.
- **Default Changed**: Code depending on the old default may break.
- **Deprecated**: Marked for removal in a future release.
- **Removed**: No longer supported.

---

## Electron 41 (Planned)

### Behavior Changed: PDFs no longer create a separate WebContents

Previously, PDF resources created a separate guest WebContents. Now PDFs render within the same WebContents using OOPIFs. Use the frame tree instead of WebContents to detect PDF resources.

### Behavior Changed: Updated Cookie Change Cause

Cookie 'changed' event causes updated:

- `inserted` — new cookie set
- `explicit` — cookie deleted
- `inserted-no-change-overwrite` — identical cookie set
- `inserted-no-value-change-overwrite` — same value, attributes updated

---

## Electron 40

### Deprecated: `clipboard` API from renderer processes

Using clipboard API directly in renderer is deprecated. Use contextBridge in preload instead.

### Behavior Changed: macOS dSYM files compressed with tar.xz

Debug symbols now use `dsym.tar.xz` instead of `dsym.zip`.

### Deprecated: `showHiddenFiles` in Dialogs on Linux

Support on Linux will be removed in Electron 42.

---

## Electron 39

### Deprecated: `--host-rules` command line switch

Use `--host-resolver-rules` instead.

### Behavior Changed: window.open popups always resizable

Per WHATWG spec, `window.open` always creates resizable popups. Restore old behavior via `setWindowOpenHandler`:

```js
webContents.setWindowOpenHandler((details) => ({
  action: 'allow',
  overrideBrowserWindowOptions: {
    resizable: details.features.includes('resizable=yes')
  }
}))
```

### Behavior Changed: NSAudioCaptureUsageDescription required for desktopCapturer (macOS ≥14.2)

Must define `NSAudioCaptureUsageDescription` in Info.plist.

### Behavior Changed: Shared texture OSR paint event data structure

The `paint` event now uses a structured `handle` property instead of separate `sharedTextureHandle`, `planes`, `modifier`.

---

## Electron 38

### Removed: `ELECTRON_OZONE_PLATFORM_HINT` env variable

`--ozone-platform` defaults to `auto`. Electron runs natively in Wayland when `XDG_SESSION_TYPE=wayland`.

### Removed: `ORIGINAL_XDG_CURRENT_DESKTOP` env variable

`XDG_CURRENT_DESKTOP` is no longer overridden.

### Removed: macOS 11 (Big Sur) support

macOS 12+ required for Electron 38+.

### Removed: `plugin-crashed` event on webContents

### Deprecated: `webFrame.routingId`

Use `webFrame.frameToken` instead.

### Deprecated: `webFrame.findFrameByRoutingId(routingId)`

Use `webFrame.findFrameByToken(frameToken)` instead.

---

## Electron 37

### Utility Process: Unhandled rejections now warn instead of crash

To restore crash behavior:

```js
process.on('unhandledRejection', () => { process.exit(1) })
```

### Behavior Changed: `process.exit()` kills utility process synchronously

### Behavior Changed: WebUSB and WebSerial Blocklist Support

Disable via `disable-usb-blocklist` / `disable-serial-blocklist` CLI flags.

### Removed: `null` value for `session` property in `ProtocolResponse`

Use `session.fromPartition(randomString)` instead.

### Behavior Changed: `BrowserWindow.IsVisibleOnAllWorkspaces()` on Linux

Now returns `false` if window is not currently visible.

---

## Electron 36

### Behavior Changed: `app.commandLine` converts to lowercase

Use `process.argv` for main process behavior control instead.

### Deprecated: `NativeImage.getBitmap()`

Use `NativeImage.toBitmap()` instead.

### Removed: `isDefault` and `status` on `PrinterInfo`

### Removed: `syncable` quota type in `Session.clearStorageData()`

### Deprecated: Extension methods on `session`

`session.loadExtension`, `session.removeExtension`, etc. moved to `session.extensions`.

### Removed: `systemPreferences.isAeroGlassEnabled()`

Always returned `true` since Electron 23.

### Changed: GTK 4 default when running GNOME

Use `--gtk-version=3` flag to work around GTK 2/3 + GTK 4 conflicts.
