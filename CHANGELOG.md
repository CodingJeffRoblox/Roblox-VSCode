# Changelog

All notable changes to the finished **Roblox VSCode** product are documented here.

---

## [1.0.1] — 2026-08-29 (Current)

### Added
- **GUI edits made directly in Studio now sync to VS Code** — *Old problem:* editing or building a ScreenGui/frame/button etc. straight in Studio had no way to reach VS Code; only the very first connection snapshot ever captured GUI content, so any GUI change made after that just never left Studio. *Now:* Studio serializes changed GUI trees to `.rbxmx` and pushes them the same way scripts are pushed, completing the direction that already existed the other way (VS Code → Studio `.rbxmx` import).
- **A new "What's New" tab in the Studio plugin**, listing what changed in the current version without needing to leave Studio.

### Fixed
- **Pushing from both sides in quick succession could silently override the other's edit, in either direction.** *Old problem:* the conflict baseline advanced whenever nothing was currently flagged as conflicted, not whenever the two sides actually agreed — a routine ordering (Studio's poll reporting its own edit slightly before the matching VS Code edit was queued) made the baseline silently adopt Studio's edit as "the agreed state" moments before a real conflict arrived, erasing it before there was anything to compare against. Studio's own `/push` also had no conflict awareness at all beyond what the poll loop had already flagged, and would overwrite on any hash difference. Both directions now correctly detect and hold back a genuine clash instead of silently discarding one side.
- **Cross-side hashes rarely actually matched.** *Old problem:* the Studio plugin's `(h * prime) % 2^32` hash step looked like correct 32-bit wraparound, but Luau numbers are IEEE-754 doubles and the product regularly needed more precision than a double keeps exactly — it silently rounded before the modulo ran, so the plugin's hash diverged from the VS Code extension's exact-wraparound hash for almost any real content (verified: even "hello world" hashed differently). Every cross-side hash comparison this fed — conflict detection, initial project sync — was comparing hashes that would almost never match. Replaced with an exact equivalent.
- **Renaming a script in Studio could leave a duplicate behind or lose the file.** The rename handler's own cleanup delete was unconditionally blocked by a guard that treated any internal call as recursion and no-opped it — the old instance was never actually removed.
- **Two instances sharing a name (Roblox's own default for a new script/folder) silently overwrote each other's synced file**, on both the Studio → VS Code and VS Code → Studio paths. Same-named-and-same-type siblings are now ranked and disambiguated ("_2", "_3") consistently on both sides.
- **A path-traversal gap in project setup** — a crafted game/folder name of "." or ".." was passed through the sanitizer untouched, which every caller then joined onto a trusted root directory. Hardened at the source, plus a defense-in-depth root check on connect.
- **Deleting a script in Studio and pressing "Push to VS Code" did nothing on the VS Code side.** Neither half of the pipeline handled a deletion — the plugin's scan only ever looked for modified/new scripts, and the extension had no delete branch to receive one anyway. Both fixed.
- **A pending conflict could get silently stomped by a manual Push**, and **a conflict that resolved itself before you picked a side** (Studio catching up on its own, etc.) used to leave a stale prompt or an empty diff behind instead of just closing quietly. Both fixed.
- **The conflict diff view's "Studio" side showed a cryptic temp file path** instead of a readable label, and those temp files were never cleaned up. Replaced with a virtual document and a clear "VS Code (left) ↔ Studio (right)" tab title — no more filesystem writes for this at all.
- **A second open VS Code window's dashboard always showed an empty/disconnected state**, even while sync was working fine in the first window — it had no way to know a host was already running elsewhere. It now mirrors the real host's data.
- **Push / Force Sync could fail outright or hang for a full 2 minutes** on a machine where VS Code's bundled ripgrep binary was missing or broken (a broken install, antivirus quarantine, etc.) — both code paths depended on it internally. Replaced with a plain recursive file scan that has no such external dependency.

## [1.0.0] — 2026-08-28

### 🎉 Out of Alpha
First stable release. This version is the result of testing the full push/pull/conflict workflow end-to-end for the first time and fixing what that testing actually found, plus a full visual redesign of the Studio plugin, extension, and website.

### Added
- **"Studio Light" redesign** — the Studio plugin, all three VS Code webview panels, and the website now share one cream/tan pastel palette instead of the old dark neon theme, including a restructured tab bar (Main/Teams visible, everything else behind a "More" dropdown) and a Credits page.
- **Roblox OAuth sign-in** (PKCE, no client secret) for a verified Teams identity instead of a self-reported username. Approved by Roblox and live as of 2026-08-28.
- **Conflicts tab (Studio plugin)** — a persistent, revisitable list of recent sync conflicts with a real line-by-line diff (added/removed highlighting), plus a banner notification when one occurs, so Studio-side users get the same visibility VS Code users already had.
- **"Show Team Members" command / `Ctrl+Alt+T`** — lists everyone on the current project's team without opening the full dashboard.
- **Teams improvements** — ownership now transfers automatically if the owner leaves and members remain (previously left the team unmanageable), the owner can remove members, and a member's stale `joinedTeamId` from a different project no longer incorrectly hides their own team.

### Fixed
- **Conflict resolution stuck in an infinite loop** — *Old problem:* the conflict detector compared Studio's current hash against the hash of the edit about to be sent, which differs on every ordinary push in flight, not just genuine conflicts - so every conflict re-appeared the instant it was resolved, forever, and the chosen version never actually got delivered except via a 2-minute give-up timeout. *Now:* it compares against Studio's hash as last actually observed, and the tracking baseline is correctly advanced the moment a conflict is resolved, so answering the prompt actually ends it.
- **"Keep Studio Version" silently discarded the Studio edit** — *Old problem:* Studio always auto-applies "VS Code wins" the instant it detects a conflict, before VS Code's interactive choice can matter - so by the time VS Code asked Studio for "its version," Studio had already overwritten it, making "Keep Studio Version" a no-op that quietly threw away the real edit. *Now:* Studio snapshots its content before the auto-overwrite and serves that back instead of the live (already-overwritten) source.
- **Scripts not refreshing in an already-open Studio editor tab** — *Old problem:* incoming syncs wrote `Script.Source` directly, which updates the underlying data but not an already-open editor tab's buffer, so it looked like sync "did nothing" until you closed and reopened the tab. *Now:* writes go through `ScriptEditorService:UpdateSourceAsync`, which live-refreshes an open tab.
- **Pushes becoming permanently unretryable** — *Old problem:* the hash a file was pushed with got marked "synced" the moment it was queued, not once delivered - so a failed or abandoned delivery looked permanently up-to-date to every future push scan, and the only way to retry was editing the file again. *Now:* that hash is rolled back when a delivery is given up on, so the next push naturally re-detects and re-queues it.
- **Unsaved-place project collision** — *Old problem:* a place that's never been saved to Roblox's servers has PlaceId 0, and every such place fell back to the exact same shared project folder, so two different unsaved test places could silently overwrite each other's synced files. *Now:* each unsaved place gets a GUID stamped onto it the first time it connects, giving it its own stable identity.
- **A Luau crash from a Roblox indexing quirk** — *Old problem:* the new diff feature's DP table assumed 0-indexed access, but `table.create(size, value)` only ever fills indices 1..size in Luau, so the very first conflict crashed applying the change entirely (and leaked its file lock, permanently blocking retries for that file) instead of rendering a diff. Fixed, plus a caller-side safety net so a future bug in that code path can no longer leak a lock.
- **Admin/profile role checks failing against a capitalized "Admin" value** — *Old problem:* `role === 'admin'` is case-sensitive, so a role manually set to "Admin" in the Firebase console (bypassing the app's lowercase-enforcing rules) locked a real admin out of the admin panel and hid their nav link. *Now:* normalized to lowercase everywhere it's compared.
- **"Last Action" always showing N/A** — *Old problem:* the dashboard's sync stats were only ever updated by the manual push command and Studio-initiated pushes, not the actual auto-sync-on-save path most edits go through. Fixed.
- Conflict-lifecycle events (detected, prompted, resolved, auto-resolved by timeout) are now also written to the visible "Roblox VSCode" Output channel, not just the hidden Extension Host developer console.

## [0.11.0-Alpha] — 2026-08-27

### Added
- **Conflict Resolution UI** — *Old problem:* if a file changed in both VS Code and Studio since the last sync, the conflicting change was silently overwritten with the VS Code version. Studio's edit was gone with no warning. *Now:* the conflicting change is held back and you're prompted to **Compare & Choose** (real diff view), **Keep VS Code Version**, or **Keep Studio Version**. Revisit anytime via "Roblox Sync: Resolve Sync Conflicts".
- **Switch Active Project** — *Old problem:* pointing the bridge at a different project meant fully restarting the server, dropping any pending sync queue. *Now:* "Roblox Sync: Switch Active Project" redirects a running bridge to a different previously-synced project (or a folder you browse to) with no restart, and warns you first if Studio looks like it's mid-sync.
- **Selective Sync UI** — *Old problem:* excluding a folder from sync meant hand-editing a raw JSON array in `settings.json`. *Now:* "Roblox Sync: Manage Selective Sync (Exclude Rules)" is a checkbox picker over your project's folders, plus custom patterns.
- **Shared Library** — *Old problem:* reusing a script across two games meant manually copy-pasting files between VS Code windows. *Now:* "Export to Shared Library" (Explorer right-click or Command Palette) saves a script/folder centrally; "Import from Shared Library" drops it into any other project so it syncs to Studio on the next push.
- **Failure reasons, not just failures** — *Old problem:* a failed file just logged "❌ Failed: file" with no indication why, so you had to guess. *Now:* the activity log and VS Code's dashboard show the actual reason (unknown root, class-creation error, bad model import, wrong instance type, etc.), and a file that's permanently given up on after repeated failures gets one final message with the last known reason instead of silently vanishing from the queue.
- **First-launch setup check** — *Old problem:* installing the Studio plugin without also installing the VS Code extension just resulted in a confusing "Connection Error" on the first Connect attempt, with no hint why. *Now:* the plugin shows a one-time popup on first launch asking whether you already have the VS Code extension, with a copyable download link if you don't.
- **Teams** — *Old problem:* there was no way to see who on your team changed what, or to keep an unregistered person from expecting sync to "just work" without setting up their own bridge. *Now:* a new Teams tab in the Studio plugin lets you create or join a team scoped to the current project (one team per project). Each person still runs their own local VS Code↔Studio bridge — this adds a shared roster and a "who did what" activity feed on top of that (self-reported Roblox username, same trust model the support ticket system already uses), plus a best-effort warning when a team owner adds someone whose Roblox VSCode install has never been seen connecting before.

### Removed
- **The Discord community server.** Replaced everywhere (site, docs, plugin description) with the [Glitch Work Studios](https://www.roblox.com/communities/35686203/Glitch-Work-Studios#!/about) Roblox community, now that the plugin is published under that group. As a side benefit, Discord invite links were also getting filtered by Roblox's own text moderation on the plugin store listing — an in-platform community link doesn't hit that filter.
- **Roblox OAuth scaffolding** — dead code: it read a settings key that didn't match anything in `package.json`, and no route was ever wired up to use it.
- **The wide-open `cors()` middleware** on the local sync server (see Fixed, below).

### Fixed
- **Stored XSS in the webviews** — *Old problem:* ticket/chat messages and other Firebase-sourced fields were rendered as raw HTML, so a message containing a script tag could execute in another user's VS Code. *Now:* all such fields are HTML-escaped before rendering.
- **Version-check false negatives** — *Old problem:* update versions were encoded as `minor × 10` for comparison, which silently breaks once a two-digit minor/major ships or a pre-release tag like "-Alpha" is present — meaning some future update notifications would never fire. *Now:* proper `major.minor.patch` parsing that ignores trailing tags, with back-compat for old numeric values already stored in Firebase.
- **Pairing brute-force gap** — *Old problem:* `/init` had no rate limit on pairing-code attempts, so a malicious local webpage could try unlimited 6-digit codes. *Now:* failed attempts are rate-limited with an escalating lockout.
- **Wide-open CORS** — *Old problem:* the local sync server enabled `cors()` for no reason nothing actually needed, letting any locally-running webpage read its responses. *Now:* removed entirely; the only real clients (Roblox Studio, this extension's own code) were never subject to browser CORS anyway.
- **Two path-safety bugs in Shared Library** — *Old problem:* entering "." as an import destination passed the containment check (it only rejected paths *outside* the root, not the root *itself*), so confirming "Overwrite" could delete your entire open project; exporting had no containment check at all, so a crafted snippet name could write outside the library folder. Both fixed.
- **Duplicate conflict-resolution requests** — *Old problem:* if Studio's response was slower than one ~150ms poll cycle, VS Code would re-request the same file's content every cycle, firing duplicate diffs or duplicate applies. *Now:* a request is only re-sent after the previous one has actually resolved.
- **Auto-resolve timeout overriding your choice** — *Old problem:* the 2-minute "give up and use VS Code's version" timer was measured from when the conflict first appeared, not from your decision — so picking "Keep Studio Version" and then waiting on a slow response could get silently discarded. *Now:* the timer resets on your decision.
- **Broken homepage release banner** — *Old problem:* `index.html` referenced `v0_10_banner.png`, which had since been renamed; the homepage's release-highlight image was silently broken. *Now:* points at the current banner.
- **Broken social-share previews site-wide** — *Old problem:* every page's `og:image`/`twitter:image` pointed at `images/update_banner.png`, a file that never existed in the repo, so sharing any link showed no preview image anywhere. Fixed across every page.
- **A real secret was being shipped in every VSIX** — *Old problem:* `.vscodeignore` never excluded `support-portal/`, so `admin_key.key` (an encryption key for the separate admin desktop app) was bundled into every packaged VSIX, including ones already distributed. *Now:* excluded from packaging, untracked from git, and the key has been rotated.
- **The Studio plugin's update-notification code referenced its own UI panel before that panel existed** — *Old problem:* `showUpdateNotification` and `openDownloadPage` referenced the plugin's dock widget at a point in the script before it was declared; in Luau that silently resolves to an undefined global instead of the real widget, so `showUpdateNotification` threw a runtime error every time an update was detected, and `openDownloadPage`'s notification frame had no parent to actually appear in. *Now:* the widget is forward-declared so every reference to it — regardless of where in the file — points at the same real widget.
- **"Download page opened in your browser!" was never true** — Studio plugins can't open a system browser; the notification claimed one had opened while a code comment above it admitted otherwise. *Now:* it shows the link in a copyable box instead of claiming something that didn't happen.

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
