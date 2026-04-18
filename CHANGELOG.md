# Changelog

All notable changes to the finished **Roblox VSCode** product are documented here.

---

## [0.10.0-Alpha] — 2026-04-18 (Current)
### UX & Documentation Overhaul
- **Interactive Walkthroughs**: Integrated native VS Code walkthroughs for step-by-step extension setup.
- **Onboarding System**: Added an interactive tutorial to the Studio plugin that highlights and explains UI components to new users.
- **Hosted Documentation**: Consistently formatted Layman, Technical, and Usage documentation now available on the official website.
- **Fluid UI Animations**: Enhanced the Studio plugin with `TweenService` for smooth tab transitions and visual feedback.
- **Expanded Settings**: Added Auto-Reconnect, Theme Cycling, and Sync Interval controls to the Studio plugin.
- **VS Code Configuration**: New workspace settings for port customization, auto-sync toggles, and improved ignore pattern support.
- **Fingerprinting Fixes**: Resolved hashing inconsistencies caused by Windows line endings (CRLF/LF) to ensure perfect differential syncing.

## [0.9.0-Alpha] — 2026-04-15
### Massive Core Overhaul (Performance & Stability)
- **Differential Sync Engine**: Fully rewritten sync core using FNV-1a (32-bit) hashing to only sync modified files.
- **Canonical Path System**: Unified path sanitization between VS Code and Roblox to eliminate path drift and special character bugs.
- **VS Code Sync Dashboard**: Brand new Webview UI featuring real-time stats, project registry, and active error reporting.
- **Conflict Management**: Implemented "IDE-Wins" conflict resolution with hash-based change awareness.
- **Class Conversion**: Studio plugin now handles Folder-to-Script conversions and vice-versa seamlessly during sync.
- **Improved Watcher**: Expanded VS Code file system watcher to track entire folder structures (`**/*`).
- **Transparency & Trust**: Added a Transparency Report to the UI and a physical toggle for Analytics opt-in.
- **Safety Backups**: Integrated `SetWaypoint` into every script update for instant rollback support in Studio.

## [0.8.0-Alpha] — 2026-04-14
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
