# git

git is everyone's favorite version control system.

## How does it work?

* Git How To: [https://githowto.com](https://githowto.com) - learn the basics
* The official Git Book: [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2) - everything you ever wanted to know about Git
* Learn Git Branching: [https://learngitbranching.js.org](https://learngitbranching.js.org) - test your knowledge without having to set up intricate and contrived repositories
* Intermediate to advanced tutorial of git and its internals: [http://think-like-a-git.net](http://think-like-a-git.net) - learn how does it actually work 
* Oh shit, git: [http://ohshitgit.com](http://ohshitgit.com) - "Mom come pick me up I'm scared"

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



