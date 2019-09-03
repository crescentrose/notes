# Assorted articles, tools and links

## Simple benchmarking

If you need to benchmark something, the fastest way is to use the `time` shell builtin. Often times you want to run the same command multiple times to account for filesystem cache warmup and eliminate statistical outliers. In those cases comparing `time` outputs and remembering to preface every command with that command can be `time` intensive \(hehe\). Instead of `time` you can use `hyperfine` which has much more options and automates much of the boring statistical work.

{% embed url="https://github.com/sharkdp/hyperfine" %}

## Better finding

The `find` command \(TODO: write article\) is a mess. There's a much more user friendly \(and faster\) alternative called `fd`:

{% embed url="https://github.com/sharkdp/fd" %}

## Fuzzy finder for the command line

Use `fzf` to:

* find files, fast \(e.g. in Vim\)
* collate completed commands \(e.g. with `C-r` in your shell\)
* procure processes \(e.g. fuzzy find the process you want to kill\)
* get general git gizmos \(e.g. branches, tags\)
* ... and [more](https://github.com/junegunn/fzf/wiki/examples)!

{% embed url="https://github.com/junegunn/fzf" %}



