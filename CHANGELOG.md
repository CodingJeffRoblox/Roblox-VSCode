# Changelog

All notable changes to the finished **Roblox VSCode** product are documented here.

---

## [0.11.0-Alpha] — 2026-08-27 (Current)
### New Features
- **Conflict Resolution UI**: When a file changed in both VS Code and Studio since the last sync, the conflicting change is now held back and you're prompted to compare & choose ("Keep VS Code Version" / "Keep Studio Version" / diff view) instead of it always being silently overwritten. Resolve pending conflicts anytime via "Roblox Sync: Resolve Sync Conflicts".
- **Switch Active Project**: New command to redirect the running bridge at a different previously-synced project (from the registry, or by browsing to a folder) without restarting the server. Warns before switching if Studio appears to be actively live-syncing.
- **Selective Sync UI**: "Roblox Sync: Manage Selective Sync (Exclude Rules)" gives a checkbox picker over your project's top-level folders (plus custom patterns) instead of hand-editing `robloxSync.ignorePatterns`.
- **Shared Library (cross-project reuse)**: "Export to Shared Library" (also in the Explorer right-click menu) copies a script/folder to a local shared library; "Import from Shared Library" drops it into any other project so it syncs to Studio on the next push.
- **Failure reasons, not just failures**: When Studio fails to apply a file (unknown root, class-creation error, model import failure, wrong instance type, etc.), the activity log now shows *why* instead of just "❌ Failed: file". The reason also round-trips to VS Code's dashboard error log, and a file that's permanently given up on after repeated failures gets one clear final message with the last known reason.

### Security & Reliability
- Fixed a stored-XSS risk: ticket/chat messages, usernames, and other Firebase-sourced fields are now HTML-escaped before being rendered in the VS Code webviews.
- Removed the Roblox OAuth scaffolding — it read from a config key that didn't match any setting and had no route ever wired to it, so it was silent dead code.
- Fixed the Studio plugin's update-version comparison, which encoded versions as `minor*10` and would compare incorrectly once a two-digit minor/major bump or a pre-release tag (e.g. "-Alpha") shipped; it now parses proper `major.minor.patch` and ignores trailing tags (with back-compat for old numeric values already stored in Firebase).
- Removed the wide-open `cors()` middleware from the local sync server — nothing legitimately needs cross-origin browser access to it, and leaving it enabled let any locally running webpage read its responses.
- Added rate-limiting/lockout to `/init` pairing-code attempts to close a blind brute-force gap.
- The sync dashboard now shows a rolling log of recent errors instead of only ever the single most recent one.
- Fixed two path-safety bugs in the new Shared Library feature: importing could previously be pointed at the project root itself (destroying the whole project on overwrite), and exporting had no containment check on the snippet name.
- Fixed conflict-resolution requests being able to fire duplicate diff/apply round trips if Studio's response was slower than one poll cycle, and fixed the auto-resolve timeout being able to silently override an already-made "Keep Studio Version" choice.
- Stopped committing `support-portal/admin_key.key` and `__pycache__/` artifacts to git.

## [0.10.0-Alpha] — 2026-04-18
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
