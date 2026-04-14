# Changelog

All notable changes to **Roblox VS Code Sync** are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).  
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.1.0] — 2025-04-14

### Added
- **Dockable Studio panel** with dark-themed UI (buttons, status, log, collaborators)
- **Bidirectional sync** — VS Code → Studio and Studio → VS Code simultaneously
- **`POST /push`** endpoint: Studio script edits are now written back to disk in real time
- **`POST /init` responds immediately** before writing files — eliminates the `HttpError: Timedout` crash
- **Status bar indicator** in VS Code: click to start/stop the server
- **`Roblox Sync: Push All Files to Studio`** command to force-push every `.lua` file
- **Keyboard shortcuts**: `Ctrl+Shift+R` (Start), `Ctrl+Shift+X` (Stop), `Ctrl+Shift+U` (Push All)
- **Team Create collaborator presence** — shared `StringValue` heartbeats in `ReplicatedStorage`
- **Activity log** in the Studio panel (last 8 timestamped events, colour-coded)
- **Auto-stop on connection loss** after 5 consecutive poll failures with a warning
- **`ChangeHistoryService` waypoints** wrap every batch of incoming IDE changes (undo support)
- **EADDRINUSE** error handling: friendly message if port 8080 is already occupied
- **File watcher deduplication**: only the latest pending change per file is sent
- **`onDidCreate` watcher**: new `.lua` files created in VS Code are also pushed to Studio

### Fixed
- File watcher was created before server started — now only attaches after successful bind
- `findScriptByPath` incorrectly resolved service names — now uses a proper `SERVICE_MAP`
- `pendingChanges` race condition between poll response and array clear — fixed with snapshot swap
- `server.listen` bound to all interfaces — now correctly binds to `127.0.0.1` only

---

## [0.0.1] — 2025-04-13

### Added
- Initial release
- `POST /init` — exports game tree from Studio to VS Code and opens a new window
- `GET /poll` — Studio polls for saved-file changes every second
- `POST /push` — stub (no-op in this version)
- Basic toolbar buttons: *Connect to VS Code*, *Toggle Live Sync*
- File watcher for `**/*.lua` files
