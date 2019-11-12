# vim

`vim` is a text editor with super powers.

## What's the point?

As opposed to most common text editors today, in which there is only one mode \(editing text\), `vim` is a _modal_ editor. This means that, when using `vim`, you are primarily _not_ entering new text, but instead executing commands \(in Normal mode\) on existing text. While this may seem counterproductive at first, it turns out it's much more common for programmers to edit existing code than it is to write new code - minor and major refactorings are more frequent than typing new code.

### Why use Vim over IDEs \(Eclipse, RubyMine\), GUI text editors \(Atom, VS Code\), other CLI editors \(nano\)?

Personal preference. Anyone who tells you anything else is lying.

## How to learn Vim?

* [http://vimcasts.org](http://vimcasts.org) - **vimcasts**, like Railscasts or Laracasts but for Vim, great for picking up new things.
* [https://thoughtbot.com/upcase/onramp-to-vim](https://thoughtbot.com/upcase/onramp-to-vim) - **Onramp to VIm by Thoughtbot** - get up to speed in less than 2 hours with clear lessons that you can take on your lunch break. This is how I learned it!
* just run `vimtutor` on the machine of your choice.

## Plugins

Everyone loves a good plugin, but some people go overboard and add plugins that reimplement Vim core functionality, unnecessarily. I try to follow [vi-improved.org's advice on plugins](https://www.vi-improved.org/plugins/): 

> We prefer no plugins if Vim can do it out of the box and furthermore prefer plugins that work with vim features rather than against them. Plugins that have persistent UI elements \(sidebars, mock-tabs, etc\) are generally frowned apon as they don't scale.
>
> **"Any sufficiently complicated set of Vim plugins contains an ad hoc, informally-specified, bug-ridden, slow implementation of half of Vim's features."** -- robertmeta's tenth rule.

That said, I do believe there is a case to be made for some of the plugins they advise against, or their "lighter" implementations, which is why you will find `fzf` and `lightline` in my [.vimrc](vim.md#vimrc).

To be fair, if you want to have your Vim install take 20 seconds to boot up and have it be a plugin-infested bugmess, more power to you, I'm not telling you how to live your life! Just try it without plugins first.

## tags

Vim understands the [ctags](https://ctags.io) format, which is basically an index of keywords in your program. For example, if you have this snippet of Ruby code:

{% tabs %}
{% tab title="sample.rb" %}
```ruby
class Hello
  def say_hi
    puts 'Hello, world!'
  end
end

hello = Hello.new
hello.say_hi

```
{% endtab %}
{% endtabs %}

After you run the `ctags` \(or, for Ruby, [ripper-tags](https://github.com/tmm1/ripper-tags)\) program to generate the index, Vim will pick up on the resulting `tags` file and let you navigate to definitions of the `Hello` class and the `say_hi` method \(default shortcut: `⌃]`\), which is especially useful if your project spans hundreds of files. You can even introspect gem sources like that. Other useful things include searching for tags and autocompletion.

While there are plugins to automatically generate the `tags` file \([gutentags](https://bolt80.com/gutentags/)\), I prefer to do it manually when I need to navigate to a tag, as I don't use that feature so often. 

You should use [a global gitignore](git.md#global-gitignore) file to hide the `tags` file from others working on the same project.

## .vimrc

The vimrc file is the preferences menu of Vim, except it's actually code that Vim executes on startup using its own language \(vimscript\). Commonly shared between nerds as a validation for spending too much time on [_ricing_](https://www.reddit.com/r/unixporn/comments/3iy3wd/stupid_question_what_is_ricing/). Your vimrc should be your own and your own alone, and you should not blindly copy someone else's, but damn if it's not fun to scroll through other people's vimrcs. In the interest of full disclosure, [here's mine](https://github.com/crescentrose/dotfiles/blob/master/vimrc).

### vimrc hacks

Assorted collection of cool tidbits you might and might not need.

{% tabs %}
{% tab title=".vimrc" %}
```text
" Use full line to separate windows
set fillchars+=vert:│

" Disable terminal bells and annoying flashing when Vim complains
set visualbell
set vb t_vb=

" [lightline] Display filename relative to Git repository root in 
" your Lightline (remember to also update the g:lightline variable
function! LightlineFilename()
  let root = fnamemodify(get(b:, 'git_dir'), ':h')
  let path = expand('%:p')
  if path[:len(root)-1] ==# root
    return path[len(root)+1:]
  endif
  return expand('%')
endfunction
```
{% endtab %}
{% endtabs %}







