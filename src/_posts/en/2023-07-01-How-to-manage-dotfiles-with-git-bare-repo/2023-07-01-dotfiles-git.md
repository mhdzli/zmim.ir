---
title: Managing Dotfiles with a Git Bare Repository
date: 2023-07-01 22:10:00 +04:30
tags: [git, dotfiles, linux]
description: One of the greatest advantages of GNU/Linux is its customizability. Many users personalize their system by modifying configuration files. One of the best methods for managing these files is using Git.
image:
toc: true
mastodon:
  host: mas.to
  username: mz
  id: 110642412995764976
lang: en
---

# Dotfiles

One of the greatest advantages of GNU/Linux is its customizability. Many users personalize their system by modifying configuration files.
As we use our system over time, these files change continuously, and when their number grows and you use them across different systems, managing them becomes more difficult.
One of the best methods for managing these files is using `Git`.

# Window Managers and Their Greatest Advantage: Customizability

I have been using GNU/Linux on my personal systems for a long time and have preferred minimal window managers over large desktop environments like GNOME and KDE for years.
Using window managers has made my system lighter and fully customized for my use.

When you use window managers instead of large desktops, you have to configure many system settings and tools yourself.

This makes you better understand the system you use and allows you to change it according to your needs.

Managing configuration files for use across different systems with different hardware and operating systems may seem difficult. Using a `Git bare repository` is one of the best and most elegant solutions for this. Since most of us already use `Git` to manage our projects, this approach requires no new tool installation or learning.

# Other Tools

Many tools exist for managing dotfiles. Perhaps the most well-known is [`GNU Stow`](https://www.gnu.org/software/stow/){:target="_blank"}{:rel="noopener noreferrer"}. A list of these tools can be found [here](https://dotfiles.github.io/utilities){:target="_blank"}{:rel="noopener noreferrer"} or on the [Arch Wiki](https://wiki.archlinux.org/title/Dotfiles#Tools){:target="_blank"}{:rel="noopener noreferrer"}.

# The `Git bare repo` Method

The first place I found this very clever method was in [this Hacker News discussion](https://news.ycombinator.com/item?id=11070797). After that I read about it in various places and decided to use this method for managing my dotfiles.

## Advantages

- No need to install extra tools
- No symlinks required
- Files are managed in a `Git` repository
- You can use different branches for different systems
- You can easily replicate your configuration when setting up a new system

## How Does It Work?

In this method, the `Git bare` repository is stored in a side folder (such as `$HOME/.cfg` or `$HOME/.myconfig`). Using a special `alias`, `Git` commands are run against this side folder and won't interfere with dotfiles or other `Git` repositories.

## Starting from Scratch

If you haven't previously tracked your configurations in a `Git` repository, you can easily start using this method with these commands:

<div class="code-block">
{% highlight bash %}
git init --bare $HOME/.cfg
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
config config --local status.showUntrackedFiles no
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/shell/.aliasrc
{% endhighlight %}
</div>

The first command creates a folder at `~/.cfg` which is a [`Git bare`](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/) repository that tracks our files.

Then we create an `alias` so that whenever we want to interact with the dotfiles repository, we use it instead of regular `git`.

We set a local flag to hide files we are not yet tracking. This way, when you type `config status` and other commands, files you don't want to track won't show up as untracked.
I prefer to use `echo "*" > ~/.gitignore` instead. With this approach, you must use the `-f` flag to add files in `config` commands, e.g.: `config add -f .dotfile`. This helps prevent accidentally adding and committing extra files with `config add .`.

I use `zsh` and have sourced the `~/.config/shell/aliasrc` file in `.zshrc`. Note that if you use a different shell, add the `alias` for that shell in the fourth line:

<div class="code-block">
{% highlight bash %}
# for bash
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.bashrc

# for zsh
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/zsh/.zshrc
{% endhighlight %}
</div>

For `fish`, you can use this function:

<div class="code-block">
{% highlight bash %}
function config -w git -d "Manages dotfiles"
    git --git-dir=$HOME/.dot --work-tree=$HOME $argv
end
{% endhighlight %}
</div>

## Installing Files on a New System or Migrating Existing Files

If you have previously stored your configurations in a `Git` repository, on a new system you can migrate to this method with the following steps:

Before installing, make sure you've added the `alias` to `.bashrc` or `.zshrc` in your repository.

<div class="code-block">
{% highlight bash %}
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.config/shell/.aliasrc
{% endhighlight %}
</div>


And that your source repository ignores the folder you're using as `git bare` to avoid strange problems: `echo ".cfg" >> ~/.gitignore` or as mentioned above: `echo "*" >> ~/.gitignore`

Now clone your configuration repository as a `bare` repository into a hidden folder at `$HOME/.cfg`:

<div class="code-block">
{% highlight bash %}
git clone --bare <git-repo-url> $HOME/.cfg
{% endhighlight %}
</div>

Define the `alias` in your current shell so you can use it:

<div class="code-block">
{% highlight bash %}
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
{% endhighlight %}
</div>

Check out the actual files using `config checkout`. I use the `home` branch in [my repository](https://github.com/mhdzli/dotfiles/tree/home){:target="_blank"}{:rel="noopener noreferrer"} to store these files, so for me the command would be: `config checkout home`. If you use different branches for different systems, make sure you check out the right files.

If there are already similar configuration files in your home directory, the above command will fail with an error.
You'll need to delete or move those files. You can list all files in the repository with this command:

<div class="code-block">
{% highlight bash %}
config ls-tree --full-tree --name-only -r <YOUR BRANCH>

#for me
config ls-tree --full-tree --name-only -r home
{% endhighlight %}
</div>

Then configure this setting to hide untracked files: `config config --local status.showUntrackedFiles no`

Congratulations! From now on you can manage your files like this:

<div class="code-block">
{% highlight bash %}
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
{% endhighlight %}
</div>

If you used `*` in `.gitignore` instead of the `status.showUntrackedFiles` setting, the commands will look like this:

<div class="code-block">
{% highlight bash %}
config status
config add -f .vimrc
config commit -m "Add vimrc"
config add -f .bashrc
config commit -m "Add bashrc"
config push
{% endhighlight %}
</div>

# Resources:

- [Hacker News](https://news.ycombinator.com/item?id=11070797){:target="_blank"}{:rel="noopener noreferrer"}
- [Atlassian's Blog](https://www.atlassian.com/git/tutorials/dotfiles){:target="_blank"}{:rel="noopener noreferrer"}
- [Coffee Addict](https://coffeeaddict.dev/how-to-manage-dotfiles-with-git-bare-repo){:target="_blank"}{:rel="noopener noreferrer"}

# [My Dotfiles](https://github.com/mhdzli/dotfiles){:target="_blank"}{:rel="noopener noreferrer"}
