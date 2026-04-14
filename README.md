# Roblox VSCode ⟨/⟩
**The ultimate real-time sync between Visual Studio Code and Roblox Studio.**

[![Version](https://img.shields.io/badge/version-0.1.0-blue.svg)](https://github.com/CodingJeffRoblox/Roblox-VSCode/releases)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/CodingJeffRoblox/Roblox-VSCode/blob/main/LICENSE)

---

## 🚀 Get Started

To use Roblox VSCode, you need two components: the **VS Code Extension** and the **Roblox Studio Plugin**.

### 1. Download VS Code Extension
Install the pre-packaged extension to enable the sync server.

*   **[Download .VSIX Extension](https://github.com/CodingJeffRoblox/Roblox-VSCode/raw/main/roblox-vscode-0.1.0.vsix)**
*   **Installation:** In VS Code, go to the Extensions tab, click the **...** (three dots) in the top right, and select **Install from VSIX**.

### 2. Download Roblox Studio Plugin
This script enables the professional sync panel inside your game.

*   **[Get SyncPlugin.lua](https://github.com/CodingJeffRoblox/Roblox-VSCode/raw/main/SyncPlugin.lua)**
*   **Installation:** Create a new `ModuleScript` or `Script` in your Studio "Plugins" folder and paste the code from the link above.

---

## 🛠️ How to Sync

#### 1. Start the Server
In VS Code, press **`Ctrl+Shift+R`**. You will see:
`✅ Roblox Sync Server Listening on Port 8080`

#### 2. Connect in Studio
1. Open your game in Roblox Studio.
2. Go to the **Plugins** tab and open **VS Code Sync**.
3. Click **⬆ CONNECT TO VS CODE**. 
   *Your project folder will be created on your desktop and automatically opened in VS Code.*

#### 3. Live Syncing
Click **▶ START LIVE SYNC** in the Studio panel.
*   **VS Code → Studio:** Save any file in VS Code to see it update in Roblox instantly.
*   **Studio → VS Code:** Edit any script in Roblox and the file on your computer updates automatically.

---

## ⌨️ Shortcuts

| Action | VS Code Shortcut |
|---|---|
| **Start Server** | `Ctrl + Shift + R` |
| **Stop Server** | `Ctrl + Shift + X` |
| **Push All Files** | `Ctrl + Shift + U` |

---

## 🛡️ Security & Privacy
*   **Local Only:** All syncing happens on your own computer (`localhost`).
*   **No Data Collection:** Your code never leaves your machine.
*   **Safe Handshake:** The `/init` process ensures folders are only created when you physically click Connect.

---

## 📄 License
This project is released under the **MIT License**.
© 2025 CodingJeffRoblox
