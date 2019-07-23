# kitty

[kitty](https://github.com/kovidgoyal/kitty) is a _"cross-platform, fast, feature full, GPU based terminal emulator_".

### Why use kitty?

* \(in theory\) faster than other terminal emulators because it relies on the GPU
* better Unicode support than iTerm2/Terminal.app \(emojis were often awkward to edit for me on iTerm2\)
* custom layouts without the need for tmux \(see [Cheat Sheet](kitty-terminal.md#cheat-sheet)\)
* extensible via plugins \(_kittens_\)
* configuration is in a file so no need to fiddle around menus when reinstalling or restoring a backup

### Cheat Sheet

All kitty commands are prefixed with ⌃⇧ \(Control-Shift\). 

| Key | Action |
| :--- | :--- |
| ⌃⇧T | Create new tab |
| ⌃⇧→ | Change current tab to the one on the right |
| ⌃⇧⏎ | Create a new split window in the current tab  |
| ⌃⇧1 | Focus first window in current tab \(2 for second, 3 for third, ...\) |
| ⌃⇧L | Toggle between different window layouts in the current tab: `stack`, `tall`, `fat`, `grid`, `horizontal`, `vertical` |
| ⌃⇧R | Resize current window manually |
| ⌃⇧U | Open Unicode character picker, allowing you to search Unicode characters \(e.g. emoji\) by name |
| ⌃⇧E | Open URL picker \(look for URLs in terminal output and allow opening of URLs given their index\) |
| ⌃⇧P&gt;F | Open file picker \(look for paths in terminal output and paste them into prompt given their index\) |

### Additional resources

* Themes: [https://github.com/dexpota/kitty-themes](https://github.com/dexpota/kitty-themes)

