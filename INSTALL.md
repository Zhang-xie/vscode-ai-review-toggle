# Install AI Review Toggle

This is a standalone, shareable VS Code extension. Nothing has been installed
into your normal VS Code profile.

1. Download or locate **ai-review-toggle-0.1.0.vsix**.
2. Open **VS Code**.
3. Press **Command+Shift+P** on Mac, or **Ctrl+Shift+P** on Windows/Linux.
4. Type **Extensions: Install from VSIX…** and select that command.
5. Select **ai-review-toggle-0.1.0.vsix**. Confirm the publisher prompt if shown.
6. Reload VS Code if requested, then open your project folder.

You should see **AI Review: OFF** at the bottom of the window.

## Use it

- Click **AI Review: OFF** before asking Codex, Gemini, or another agent to edit.
- Wait for the baseline-ready notification, then run your agent.
- Open **Explorer → AI Review** and click a changed file for its before/after diff.
- Use **Keep change / Reject change** above edited blocks. Sidebar icons also resolve entire files.
- Pause the agent before rejecting changes. Save the editor after a rejection.
- Turn review **OFF** when finished. Normal editing then has no review buttons.

Mac toggle shortcut: **Command+Option+Shift+R**.
Windows/Linux: **Ctrl+Alt+Shift+R**.

While ON, the extension includes your manual edits as well as the agent's edits.
If you modify an agent's change, you review the final combined block.

## Share it

Send others the same `.vsix` file. They follow the installation steps above.
They do not need Node.js, Python, API keys, or the source archive.

The source archive includes the MIT license, tests, developer instructions,
and a Python packaging script. It is not published to the Marketplace.

## Release status

Version 0.1.0 is a preview. Thirty automated tests passed, and VS Code's CLI
accepted the package in an isolated temporary profile. The actual editor UI
could not be launched for end-to-end verification in the build environment.
See `ai-review-toggle/VALIDATION.md` in the source archive for details.
