# Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com) is a free source-code editor made by Microsoft for Windows, Linux and macOS. Features include support for debugging, syntax highlighting, intelligent code completion, snippets, code refactoring, and embedded Git. Users can change the theme, keyboard shortcuts, preferences, and install extensions that add additional functionality.

## Installation

**Download:** [https://code.visualstudio.com/](https://code.visualstudio.com/)

- **Windows:** Download and run the installer. During setup, it's worth checking the boxes for **"Add to PATH"** and **"Register Code as an editor for supported file types"** if offered — both are usually checked by default.
- **macOS:** Download the `.zip`, unzip it, and drag **Visual Studio Code.app** into your **Applications** folder.
- **Linux:** Download the `.deb` (Debian/Ubuntu) or `.rpm` (Fedora/RHEL) package from the download page, or install via your distribution's package manager if available.

Confirm installation by opening VS Code — you should see the Welcome tab.

## Required Extension: Live Server

**Live Server** is required for this course. It lets you preview your HTML/CSS/JavaScript web mapping projects locally in your browser, with the page automatically refreshing every time you save a file — essential for the labs starting in Week 4.

**To install:**

1. Open VS Code.
2. Click the **Extensions** icon in the left sidebar (four squares icon), or press `Ctrl+Shift+X` (`Cmd+Shift+X` on macOS).
3. Type **Live Server** into the search bar.
4. Select the extension by **Ritwick Dey** and click **Install**.

**To use it:** open a project folder containing an `.html` file, right-click the file in the Explorer panel, and choose **"Open with Live Server."** Your default browser should open automatically and show the page.

## Important Extensions

- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
- **Live Server** (required — see above)

## Useful Extensions

- autoDocstring
- Black Formatter
- Code Runner
- CodeSnap
- GistPad
- [GitHub Actions](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-github-actions)
- GitHub Copilot
- GitHub Copilot Chat
- GitHub Pull Requests
- GitLens
- IntelliCode
- Markdown All in One
- Markdown Shortcuts
- Prettier
- Pylint
- vscode-icons

## Keyboard shortcuts

1. **Command Palette:** `Ctrl+Shift+P`
2. **Search and open a file:** `Ctrl+P`
3. **Go to Symbol:** `Ctrl+Shift+O`
4. **Move line up and down:** `Alt+Arrow up/down`
5. **Go to line number:** `Ctrl+G`
6. **Cut the Entire Line:** `Ctrl+X`
7. **Select the entire line:** Triple-clicking at the line of the line
8. **Markdown preview:** `Ctrl+K V`
9. **Zen Mode:** `View > Appearance > Toggle Zen Mode`
10. **Split view:** `View > Editor Layout > Split Up`
11. **Edit multiple lines simultaneously**: Hold **Alt** and **click** to add a cursor in the places you want to type or edit the code.

## Verify Your Setup

- [ ] Opening VS Code shows the Welcome tab with no error messages
- [ ] The Extensions panel shows **Live Server** as installed
- [ ] Right-clicking an `.html` file shows an **"Open with Live Server"** option, and selecting it opens the page in your browser at an address like `127.0.0.1:5500`

If any of these fail, try restarting VS Code, or reach out during office hours before the first lab.

## References

- [Visual Studio keyboard shortcuts for Windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)
- [Visual Studio keyboard shortcuts for macOS](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
