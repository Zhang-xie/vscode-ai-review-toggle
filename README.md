# AI Review Toggle

A standalone VS Code extension that adds one review switch to your existing
coding-agent workflow (which mimics the function in Cursor IDE). Turn it on before asking an agent to edit, inspect the
changes, and keep or reject individual blocks. Turn it off for normal editing.
It works independently of the agent: Codex, Gemini, terminal tools, and other
extensions can continue editing the same folder.

**Preview release: 0.1.0.** Local trusted folders, VS Code 1.95 or later, desktop
Windows/macOS/Linux. No API key, subscription, Node.js installation, or separate
server is needed to use the packaged extension.

## Install the shareable VSIX

1. Save `ai-review-toggle-0.1.0.vsix` somewhere on your computer.
2. Open VS Code and press **Command+Shift+P** on Mac or **Ctrl+Shift+P** on Windows/Linux.
3. Run **Extensions: Install from VSIX…**.
4. Select the `.vsix` file. Confirm the publisher prompt if VS Code displays one.
5. Reload VS Code if prompted, then open your project folder.
6. Look for **AI Review: OFF** in the bottom status bar.

You can also install with `code --install-extension /path/to/ai-review-toggle-0.1.0.vsix`.
The publisher identifier is `local-review-tools`; this package is privately
shareable and has not been published to the Marketplace.

## Everyday workflow

1. Click **AI Review: OFF**. Wait for the **baseline ready** notification before starting your agent.
2. Ask your agent to edit. The status bar shows **AI Review: ON · N**, where N is the number of pending files.
3. In the Explorer sidebar, expand **AI Review**. Click a file to see its before/after diff.
4. In an edited file, click **Keep change** or **Reject change** above each block. The sidebar's ✓ / discard icons resolve an entire file. Hover the icons to see their labels.
5. **Pause or finish the agent before rejecting edits.** A filesystem observer cannot atomically lock out another process that is still writing.
6. Save the file after rejecting text changes. Rejections are normal, undoable VS Code editor edits and deliberately do not auto-save unrelated work.
7. Click the status-bar toggle to finish. If decisions remain, **Keep Everything & Turn Off** retains the files as they currently are and clears the snapshot. Cancel leaves review on.

Shortcut: **Command+Option+Shift+R** on Mac; **Ctrl+Alt+Shift+R** elsewhere.
The command palette also provides **AI Review: Toggle On / Off**, **Open Changes**,
**Refresh Changes**, and **Keep All Changes**.

To reject multiple files, use their individual Reject controls before turning
review off. There is intentionally no bulk destructive Reject All operation.

## What Keep and Reject mean

- **Keep** accepts the current change into the review baseline. It does not commit to Git or save your editor buffer.
- **Reject** restores the selected block from that baseline. Other separate blocks stay intact.
- For a **new file**, Reject asks before removing the file. Save or undo dirty edits first.
- For a **deleted file**, Reject asks before recreating its baseline content.
- A rename appears as a deletion plus a creation; review both entries.
- Pure deletions have a control at the nearest surviving line. The diff shows the removed lines.

While review is **OFF**, normal edits produce no review buttons. While it is
**ON**, both manual edits and agent edits are included. If you manually adjust an
agent's change, the final combined block is what you keep or reject. This version
does not claim to identify which person or process authored each line.

An old button is refused when the file or baseline has changed since the button
was generated. Review the refreshed diff and click again. This protects against
stale review actions; it is not a lock against simultaneous external writes.

## Limits and settings

This version reviews text files in local workspace folders. It does not review
binary files, symlinks, remote workspaces, or files outside the opened folders.
Unsaved **Untitled** documents are not tracked. Standard UTF-8 files are supported;
non-UTF-8 / BOM files read directly from disk are skipped. Open text buffers are
reviewed using VS Code's decoded text.

Defaults:

- Maximum file size: **256 KB**.
- Maximum baseline text: **32 MB**.
- Maximum candidate files: **5,000**; start/refresh fails with an explicit error above this limit.
- Common dependency/build folders are excluded: `.git`, `node_modules`, `.venv`, `venv`, `dist`, `build`, `out`, `coverage`, `.next`, `.cache`, `__pycache__`, `vendor`.

Search VS Code Settings for **AI Review** to change the exclusion glob or size
limits. Settings are captured at session start; start a new session to apply
changes. A warning count identifies unsupported/unreadable candidate files.
Excluded folders are not scanned or included in that count. If a tracked file
becomes too large or unreadable, its decisions are unavailable until it is
reviewable again. For broad rewrites, adjacent changes may appear as one large
review block to keep diff computation bounded.

## Storage and recovery

An active session's baseline is stored in this extension's local VS Code
workspace storage, outside your project. It includes the text of eligible files,
including pre-existing unsaved edits. Treat it with the same privacy as your
source code. Nothing is uploaded. No telemetry, model calls, shell execution,
or network requests are implemented by the extension.

After a VS Code reload, the baseline restores and changes are compared against
current files and recovered editor buffers. Ending a session clears the active
snapshot. This is review state, not a backup system; use version control as usual.
Only one review session exists per VS Code workspace. Do not run concurrent
sessions for the same folder in multiple windows.

## Develop, test, and package

The runtime uses only VS Code and Node built-ins: there is no dependency install
or compilation step.

- Open the `ai-review-toggle/` source folder in VS Code and press **F5** for an Extension Development Host.
From the repository root:

```sh
cd ai-review-toggle
npm test
python3 scripts/package.py
```

Tests require Node.js 20+; packaging requires Python 3 and writes
`ai-review-toggle-0.1.0.vsix` to the repository root.
- For an isolated real-editor test, run `scripts/test-host.sh /absolute/path/to/empty-test-directory` with `code` on PATH. You can set `CODE_BIN` to the full CLI path. It uses a separate profile and extensions directory and does not install into your normal profile. The report is written to `report.json` in the test directory.

The development-host test is supplied but could not be completed in the build
environment because its GUI process failed to launch. See [VALIDATION.md](ai-review-toggle/VALIDATION.md) for
the checks actually performed.

## Repository contents

| Path | Contents |
| --- | --- |
| `ai-review-toggle/` | Extension source, tests, and packaging scripts |
| `ai-review-toggle-0.1.0.vsix` | Installable preview package |
| `ai-review-toggle-source-0.1.0.zip` | Source archive |
| `SHA256SUMS.txt` | Checksums for the distributed artifacts |
| [INSTALL.md](INSTALL.md) | Short installation guide |

## Share or publish

You may distribute the `.vsix` directly. Recipients use **Install from VSIX…**;
they do not need the source code or any development tools. The MIT license also
allows modification and redistribution.

For Marketplace publication, create your own publisher identity, replace
`publisher` in `package.json` with that identifier, add your repository/support
URLs, and increment the version for each release. Use Microsoft's `@vscode/vsce`
tooling to validate and publish under your own account. The placeholder publisher
here is not a registered Marketplace account or a claim of verified publisher
status. No publication is performed by the included packaging script.

## Troubleshooting

- **No toggle:** reload the window, open a local trusted folder, and check that the extension is enabled.
- **No inline buttons:** enable VS Code's **Editor: Code Lens** setting; use the sidebar controls as a fallback.
- **No changes:** review only tracks differences since turning ON, not edits from before the snapshot. Check exclusions and the skipped-file warning.
- **Stale change warning:** let the agent stop writing, refresh, and review again.
- **Rejected code looks unchanged on disk:** save the editor buffer. Rejections intentionally remain unsaved.
- **Remove extension:** open Extensions, find **AI Review Toggle**, and choose Uninstall.

## License

[MIT](ai-review-toggle/LICENSE).
