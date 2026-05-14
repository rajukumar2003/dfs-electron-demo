# DFS Dashboard — Electron Demo

Throwaway Electron shell that validates the **Windows install + first-launch UX** for the DFS offline dashboard packaging approach. Not the real app — a minimal Cloudify-branded window so we can hand Steve a `.exe` and see exactly what DFS will see on day one.

## What this proves

- Steve double-clicks the installer → walks through the unsigned-installer SmartScreen flow (*"More info → Run anyway"*) → installs → launches → sees a working window.
- A Cloudify-branded window opens via the Windows Start menu / desktop shortcut.
- Uninstall via Add/Remove Programs works cleanly.

What it **doesn't** prove: Next.js inside Electron, SQLite path resolution, the real dashboard rendering. Those are the production project (~27h estimated separately).

## Run locally (Mac dev)

```bash
npm install
npm start
```

## Build the Windows installer

Builds run on GitHub Actions (`windows-latest`) — not on this Mac. Wine cross-builds work but are flaky for native modules and won't be needed once the real project pulls in `better-sqlite3`. Same pipeline carries over.

```bash
git push origin main          # → triggers .github/workflows/build-windows.yml
```

Then open the GitHub Actions tab, wait ~3 min, download the artifact `dfs-dashboard-demo-windows`. Inside is `DFS Dashboard Demo-0.1.0-Setup.exe`.

## The flow Steve walks through on Windows

1. Double-click `DFS Dashboard Demo-0.1.0-Setup.exe`
2. "Windows protected your PC" → **More info** → **Run anyway**
3. NSIS installer asks for install location → **Install**
4. App launches automatically (or via Start menu / desktop shortcut)
5. Uninstall via Settings → Apps if desired

## Layout

```
src/main.js               Electron main process — window setup, CSP, external-link handling
src/renderer/             Static HTML/CSS — the visible window
build/icon.{png,ico}      Cloudify cloud icon, 256×256
.github/workflows/        Windows installer build pipeline
```

## Notes

- Unsigned NSIS installer — the SmartScreen prompt is expected. Real project will need the same flow (or Azure Trusted Signing if DFS asks for warning-free first-launch).
- No auth, no data, no network calls. Strict CSP locks the renderer down.
