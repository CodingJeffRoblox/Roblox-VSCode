# Changelog

All notable changes to the finished **Roblox VSCode** product are documented here.

---

## [0.8.0-Alpha] — 2026-04-14 (Current)
### Added
- **Official Alpha Release**: Transitioned project from "Testing" to "Alpha".
- **Bi-Directional Sync**: Added "Push to VS Code" button in Studio to overwrite IDE files with Studio code.
- **Improved Discord Webhooks**: Switched to `PostAsync` for higher reliability and added error diagnostics.
- **Progress Tracking**: Added live percentage and filename readouts during synchronization.

## [0.6.0-Testing] — 2026-04-14
### Added
- **Multi-Window Intelligence:** You can now "Push" and "Sync" from any VS Code window. The extension automatically detects if a server is already running and connects to it as a client.
- **Real-Time Script Updating:** Changes pushed from VS Code now actually overwrite the Source in Studio scripts!
- **Scrolling Activity Log:** The Studio plugin log box now supports scrolling and auto-scrolls to the latest update.
- **Discord Webhook Notifications:** Successful connections now send a beautiful status embed to your Discord server (obfuscated for security).
- **Better Naming:** Improved game name detection (e.g., properly identifying "Plane Merge Tycoon").
- **Automatic Undos:** Studio now records a "VSCode Sync Update" waypoint so you can undo syncs with Ctrl+Z.

### Fixed
- **"Server Already Running" Error:** Eliminated the crash/error when multiple windows were open.
- **"Cannot find name context" Error:** Fixed a syntax bug in the activation logic.
- **Dependency Issues:** Switched to standard Node.js libraries to ensure zero-install portability.

---

## [0.1.0-Testing] — 2026-04-14
### Added
- **Professional Studio Panel:** A dockable dark-themed UI with status, log, and action buttons.
- **Bidirectional Sync:** Edit in VS Code to update Roblox, or edit in Roblox to update VS Code files instantly.
- **Anti-Spam Logic:** Connect and Sync buttons now have a "Waiting..." state to prevent accidental spam clicks.
- **Custom Icon Support:** Added support for the professional "Brackets & Bolt" logo.
- **Project Auto-Creation:** Syncing now automatically creates and opens your project in `Documents/RobloxProjects`.
- **Activity Log:** Real-time feedback inside the plugin panel.

### Fixed
- **Connection Reliability:** Fixed the "HTTP Timeout" crash when connecting large games.
- **Sync Accuracy:** Improved folder and script name resolution to prevent "Script not found" errors.
- **UI Scaling:** Fixed overlapping buttons and text in the Studio plugin.

---

## [0.0.1-Testing] — 2026-04-13
### Added
- Initial alpha release.
- Basic script sync logic.
- Support for Server, Client, and Module scripts.
