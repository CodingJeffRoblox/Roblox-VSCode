# Contributing

Thank you for your interest in improving **Roblox VS Code Sync**! Here's how to get started.

## Development Setup

### Prerequisites

- [Node.js](https://nodejs.org/) 18+
- [VS Code](https://code.visualstudio.com/) 1.80+
- [Roblox Studio](https://www.roblox.com/create) with HTTP Requests enabled

### Clone & Install

```bash
git clone https://github.com/CodingJeff/roblox-vscode-sync
cd roblox-vscode-sync
npm install
```

### Build

```bash
# Development build (with source maps)
node esbuild.js

# Watch mode (rebuilds on save)
node esbuild.js --watch

# Production build
node esbuild.js --production
```

### Run & Debug

1. Open the `roblox-vscode-sync` folder in VS Code
2. Press `F5` to launch the **Extension Development Host**
3. In the new VS Code window, open the Command Palette and run `Roblox Sync: Start Server`
4. Open Roblox Studio and test the plugin

### Package for Distribution

```powershell
# PowerShell (use ; not &&)
node esbuild.js ; npx @vscode/vsce package --allow-missing-repository
```

## Project Structure

```
roblox-vscode-sync/
├── src/
│   └── extension.ts       # VS Code extension entry point
├── studio-plugin/
│   └── SyncPlugin.lua     # Roblox Studio plugin
├── out/                   # Compiled JS (generated)
├── .vscode/
│   ├── launch.json        # Debug config
│   ├── tasks.json         # Build tasks
│   └── settings.json
├── esbuild.js             # Build script
├── package.json           # Extension manifest
├── tsconfig.json
└── README.md
```

## API Endpoints (localhost:8080)

| Method | Path | Direction | Description |
|---|---|---|---|
| `POST` | `/init` | Studio → VS Code | Export full game tree, open workspace |
| `GET` | `/poll` | VS Code → Studio | Studio polls for pending file changes |
| `POST` | `/push` | Studio → VS Code | Push Studio script edits to disk |

## Coding Guidelines

- TypeScript for the VS Code extension (`strict` mode)
- Lua for the Studio plugin (compatible with Roblox's Luau runtime)
- Keep HTTP calls in Studio wrapped in `pcall` — network errors must never crash the plugin
- All `/init` disk I/O must remain asynchronous (respond before writing)

## Submitting a Pull Request

1. Fork the repository
2. Create a branch: `git checkout -b feat/my-feature`
3. Commit with a clear message: `git commit -m "feat: add X"`
4. Push and open a PR against `main`

Please include a description of what changed and why.

## Reporting Issues

Open a [GitHub Issue](https://github.com/CodingJeff/roblox-vscode-sync/issues) with:
- VS Code version
- Roblox Studio version
- Steps to reproduce
- Error messages from the Output panel (`Ctrl+Shift+U` → *Roblox Sync*)
