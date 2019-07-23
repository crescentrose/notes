# git

git is everyone's favorite version control system.

## Global .gitignore

There is nothing that annoys me more than cloning a bunch of `.vscode`, `.atom`, `.idea` and other directories alongside an interesting project. To keep your personal preferences from infiltrating everyone else's systems, you should use a global `.gitignore` file, which I like to place in my `$HOME` as `.gitignore_global` \(but if you have a Linux system you should probably put it in your `~/.config` directory\).

Run the following command to introduce `~/.gitignore_global` to Git:

`git config --global core.excludesfile ~/.gitignore_global`

 Then you can use the familiar .gitignore syntax to ignore commonly ignored files:

```text
.vscode
tags*
.DS_Store
```



