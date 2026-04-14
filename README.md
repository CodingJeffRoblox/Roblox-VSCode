# Roblox VS Code Sync

> **Real-time bidirectional sync between VS Code and Roblox Studio.**  
> Edit your game scripts in VS Code — changes appear in Studio instantly. Edit in Studio — files update on disk automatically.

---

## Features

| Feature | Description |
|---|---|
| ⬆ **Connect** | Exports your entire game script tree from Studio to a local folder and opens it in a new VS Code window |
| ▶ **Live Sync** | Bidirectional: save a `.lua` file in VS Code → applied in Studio; edit a script in Studio → written to disk |
| ⟳ **Push All** | Force-push every Lua file in the workspace to Studio in one click |
| 👥 **Collaborator Presence** | See which Team Create members also have the plugin open, and whether they're syncing |
| 📋 **Activity Log** | Live timestamped log inside the Studio panel showing every sync event |
| ⌨️ **Keyboard Shortcuts** | Start / stop / push without touching the mouse |

---

## Requirements

- **VS Code** 1.80.0 or later  
- **Roblox Studio** with HTTP Requests enabled  
  *(Game Settings → Security → Allow HTTP Requests → ✅)*
- The **Studio Plugin** installed in Roblox Studio (see below)

---

## Quick Start

### 1 · Install the VS Code Extension

Install this extension from the marketplace or via the `.vsix` file:

```
code --install-extension roblox-vscode-0.1.0.vsix
```

### 2 · Install the Studio Plugin

Copy `studio-plugin/SyncPlugin.lua` into Roblox Studio as a Plugin script, or install it from the [Roblox Plugin Marketplace](#) *(link coming soon)*.

> **Enable HTTP Requests in Studio:**  
> Game Settings → Security → Allow HTTP Requests → ON

### 3 · Start the Server

In VS Code press **`Ctrl+Shift+R`** (Mac: `Cmd+Shift+R`), or open the Command Palette (`Ctrl+Shift+P`) and run:

```
Roblox Sync: Start Server
```

The status bar at the bottom of VS Code will show:  
`$(broadcast) Roblox Sync: Running`

### 4 · Connect from Studio

In Roblox Studio, open the **VS Code Sync** panel (toolbar → *VS Code Sync*) and click **⬆  Connect to VS Code**.

Studio will export your game's script tree, and VS Code will open a new window with your project.

### 5 · Start Live Sync

Click **▶  Start Live Sync** in the Studio panel. Now:

- **Save any `.lua` file** in VS Code → the change applies to Studio in under a second
- **Edit any script in Studio** → the file on disk is updated automatically

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+R` | Start Sync Server |
| `Ctrl+Shift+X` | Stop Sync Server |
| `Ctrl+Shift+U` | Push All Files to Studio |

---

## Studio Panel

The plugin creates a **dockable dark-themed panel** in Studio with:

- **Status indicator** — dot shows Disconnected (🔴) / Connected (🟡) / Live Syncing (🟢)
- **Action buttons** — Connect, Start Sync, Stop Sync, Push All
- **Collaborators** — shows Team Create members who also have the plugin active
- **Activity log** — last 8 timestamped events

---

## How It Works

```
VS Code Extension          Studio Plugin
     │                          │
     │   HTTP on localhost:8080 │
     │◄─────────────────────────┤  POST /init   (export game tree)
     │──────────────────────────►│  { status: ok, path }
     │                          │
     │◄── FileWatcher ──────────┤  GET  /poll   (IDE → Studio, every 1s)
     │──────────────────────────►│  { changes: [...] }
     │                          │
     │◄─────────────────────────┤  POST /push   (Studio → IDE, on script edit)
     │──────────────────────────►│  { status: ok }
```

All traffic is **localhost only** — nothing leaves your machine.

---

## Extension Commands

| Command | Description |
|---|---|
| `Roblox Sync: Start Server` | Start the local HTTP sync server on port 8080 |
| `Roblox Sync: Stop Server` | Stop the server and dispose the file watcher |
| `Roblox Sync: Push All Files to Studio` | Queue every `.lua` file in the workspace for Studio to pick up |

---

## Supported Script Types

| Roblox Class | File Extension |
|---|---|
| `Script` | `.server.lua` |
| `LocalScript` | `.client.lua` |
| `ModuleScript` | `.lua` |

Scripts with children are written using the **init pattern**:  
`MyScript/init.server.lua` + child files alongside it.

---

## Troubleshooting

### `HttpError: Timedout` in Studio

- Make sure the VS Code server is running (`Ctrl+Shift+R`) **before** clicking Connect in Studio
- Check that HTTP Requests are enabled in Game Settings

### Port 8080 already in use

Another process is using port 8080. Stop it, or the extension will show an error in the status bar.

### Script not found after syncing

The file path in VS Code must match the Roblox hierarchy exactly.  
Try the **⟳ Push All** button to re-sync everything from scratch.

### Changes not applying in Studio

Make sure **Live Sync is active** (🟢 in the Studio panel). The server must be running in VS Code **and** sync must be started in Studio.

---

## Known Limitations

- Script **creation and deletion** from VS Code is not yet synced (renames require a fresh Connect)
- Only script-bearing instances are exported (UI, Physics objects etc. are skipped)
- Runs on `localhost` only — remote collaboration requires a tunnel (e.g. ngrok)

---

## License

MIT © 2025 — see [LICENSE](LICENSE) for details.
