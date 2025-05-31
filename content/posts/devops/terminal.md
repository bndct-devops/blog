---
date: '2025-05-29T17:15:24+02:00'
draft: false
title: 'My ever changing terminal setup'
description: "It's the first terminal post but won't be the last one."
tags: ["devops", "terminal", "shell", "zsh", "starship", "ghostty"]
categories: ["DevOps", "Terminal", "Shell", "Zsh", "Starship", "Ghostty"]
author: ["benedict"]
showToc: true
TocOpen: false
cover:
  image: "https://blog.bndct.dev/images/cover_ghostty_cat.png"
---

## My Terminal Setup: Zsh, Ghostty, Starship and Catppuccin

As nerds, the terminal is where we would like to spend most of the time. It's where we build, deploy, and explore. But it's not always that easy. A well-tuned terminal isn't just about aesthetics; it's about efficiency, clarity, and making every command a little more enjoyable. This is what I use (at the moment, might change in a week).

### Ghostty: Minimal, Pretty and Fast

I've been on **iTerm** as long as I can think - okay, just 3 years ago when I got my first MacBook. It served me well and I can recommend it without hesitation. But sometimes one likes to try out something new. But only one got stuck after trying it out for a few weeks. For me, that was [**Ghostty**](https://ghostty.org/).

## Why Ghostty?

I appreciate Ghostty for its performance, look and config based settings. It feels snappy and responsive, which is crucial when you're spending hours in the command line.

### The Catppuccin Touch

Aesthetics play a big role for me when I spend dozens of hours a week in my terminal. I'm a huge fan of the **Catppuccin Mocha** theme. Its vibrant yet soothing color palette reduces eye strain and makes the terminal a pleasure to look at. Recently I even switched this very blog to it. My **Ghostty** config file is pretty slim.

Here's my **Ghostty** configuration, located at `~/.config/ghostty/config`:

```config  
font-family = MesloLGS Nerd Font Mono
font-size = 12
background-opacity = 0.85
theme = catppuccin-mocha
```

*   **font-family = MesloLGS Nerd Font Mono**: A **Nerd Font** is essential imo, as it includes thousands of glyphs, icons, and ligatures used by various command-line tools and prompts.
    
*   **font-size = 12**: Pretty self explanatory.
    
*   **background-opacity = 0.85**: I love a subtle transparency!
    
*   **theme = catppuccin-mocha**: Explicitly sets the beautiful Catppuccin Mocha theme.

## Starship: My Customizable Prompt

Like with **iTerm** before, I've been using [**Powerlevel10k**](https://github.com/romkatv/powerlevel10k) even longer then **iTerm**. But, sometimes you gotta embrace change. The reason again being wanting something more minimal.
After testing several out, I started using **Starship**. A fantastic cross-shell prompt that's incredibly fast and infinitely customizable.

### Why Starship?

It's written in Rust (Reddit tells me that makes it cool), making it lightning fast, and its modular design means you only enable what you need.

![Terminal](images/ghostty.png)

## Zsh: The Powerhouse Shell

While Bash is good, **Zsh** takes the command-line experience to a whole new level. Its advanced tab completion, powerful plugin ecosystem, and extensive customization options make it my shell of choice. Being the default shell on MacOS since a few years also helps.

### Diving into my .zshrc

My `.zshrc` is the heart of my terminal configuration, defining functions, loading plugins, and setting up aliases. I've structured it logically (imo) to ensure dependencies are met and performance is optimized.

Here’s the complete `.zshrc` file:

```bash
# ~/.zshrc

# 1. Environment Variables
export XDG_CONFIG_HOME="$HOME/.config"
export EDITOR="nvim"

# 2. Path Adjustments (if needed, typically in .zprofile)
# export PATH="/opt/homebrew/bin:$PATH"

# 3. Zsh Plugin Managers
# Not a fan, prefer handling them manually

# 4. Core Zsh Completions/Utilities
autoload -U compinit
compinit

# 5. Tool Initializations
eval "$(zoxide init zsh)"

# 6. Zsh Plugins
source "$(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh"
source "$(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh"
source "$(brew --prefix)/share/zsh-autocomplete/zsh-autocomplete.plugin.zsh"
source ~/.config/fzf-tab/fzf-tab.plugin.zsh

# 7. Prompt Initialization
eval "$(starship init zsh)"

# 8. Functions
function current_branch() {
  ref=$(git branch --show-current 2> /dev/null) || return
  echo "${ref}"
}

function y() {
  local tmp="$(mktemp -t "yazi-cwd.XXXXXX")" cwd
  yazi "$@" --cwd-file="$tmp"
  IFS= read -r -d '' cwd < "$tmp"
  [ -n "$cwd" ] && [ "$cwd" != "$PWD" ] && builtin cd -- "$cwd"
  rm -f -- "$tmp"
}

# 9. Aliases

# Shortcuts
alias starconf="nvim ~/.config/starship.toml"
alias ghostconf="nvim ~/.config/ghostty/config"

# Zsh
alias zrc="code ~/.zshrc"
alias rzsh="source ~/.zprofile && source ~/.zshrc"

# Git
alias g="git"
alias gst="git status"
alias ga="git add"
alias gc="git commit"
alias gp="git push"
alias gl="git pull"
alias gco="git checkout"
alias gd="git diff"
alias ggpush="git push origin $(current_branch)"

# Eza
alias ls="eza --icons"
alias l="eza -lbF --git"
alias ll="eza -l --icons"
alias llm="eza -lbGd --git --sort=modified"
alias la="eza -la --icons"
alias lx="eza -lbhHigUmuSa@ --time-style=long-iso --git --color-scale"

# 10. Tool Extensions (fzf-enhanced tools, history managers)
eval "$(atuin init zsh)"

if [ -f "$(brew --prefix)/share/forgit/forgit.plugin.zsh" ]; then
  source "$(brew --prefix)/share/forgit/forgit.plugin.zsh"
fi
```


I know it's a lot, but lets break it down:

#### 1\. Environment Variables

These are set first as many tools and configurations rely on them.

*   **`export XDG\_CONFIG\_HOME="$HOME/.config"`**: This aligns with the XDG Base Directory Specification, keeping my home directory clean by moving configuration files into a standardized `.config` folder.
    
*   **`export EDITOR="nvim"`**: Setting `nvim` (Neovim) as my default editor ensures that any command-line tool that needs to open a text editor will use my preferred setup. Also I don't get made fun off for using `nano` anymore.
    

#### 2\. Core Zsh Completions/Utilities

*   **`autoload -U compinit`** and **`compinit`**: These commands enable and initialize Zsh's powerful completion system. This is a prerequisite for many plugins that enhance tab completion.
    

#### 3\. Tool Initializations

These commands initialize tools that provide commands or modify shell behavior.

*   **`eval "$(zoxide init zsh)"`**: **Zoxide** is a smarter cd command. It learns your most frequently used directories and allows you to jump to them with just a few keystrokes using a fuzzy search. This line initializes zoxide for Zsh.
    

#### 4\. Zsh Plugins

Plugins significantly extend Zsh's functionality. I've ordered them based on common dependencies and best practices.

*   **zsh-autosuggestions**: This plugin suggests commands as you type, based on your command history, making your terminal sessions much faster and reducing typos.
    
*   **zsh-syntax-highlighting**: Provides syntax highlighting for the shell script you're typing. Commands light up green if they're valid and red if they're not, offering immediate visual feedback.
    
*   **zsh-autocomplete**: This enhances Zsh's already powerful completion with live, inline suggestions and a more robust completion menu.
    
*   **fzf-tab**: This plugin integrates the fantastic fuzzy finder **fzf** into Zsh's tab completion, providing an interactive selection menu for files, directories, and more.
    
*   **forgit**: This provides a collection of interactive Git commands built on fzf, making common Git operations like git add, git log, and git diff much more intuitive and visual.
    

#### 5\. Prompt Initialization

*   **eval "$(starship init zsh)"**: **Starship** is loaded last to ensure it correctly overrides or integrates with any prompt modifications made by earlier plugins, giving me a consistent and information-rich prompt.
    

#### 6\. Custom Functions

These are custom shell functions that streamline specific workflows.

*   **current\_branch**: A simple but incredibly useful function that echoes the current Git branch name. Important for my dear `ggpush` command I copied from my time with `oh-my-sh`.
    
*   **y**: This function is for integrating with **Yazi**, a fantastic terminal file manager. It allows Yazi to change your current working directory in the shell, a feature not natively available with most terminal file managers.
    

#### 7\. Aliases

Aliases are shortcuts for longer commands, significantly improving typing efficiency. I've grouped them by category.

*   **Shortcuts**: Quick access to my Starship and Ghostty config files for easy editing.
    
*   **Zsh**: Aliases for managing my Zsh configuration, like `rzsh` to quickly reload my shell.
    
*   **Git**: Shorthands for common Git commands like `gst` (git status), `ga` (git add), and `ggpush` (git push to current branch).
    
*   **Eza**: I've replaced the default ls with **Eza**, a modern ls replacement that offers better defaults, more features, and beautiful icons. My aliases provide various listing formats for different needs.
    
    *   ls='eza --icons': ls with icons.
        
    *   l='eza -lbF --git': A compact long list with file type indicators and Git status.
        
    *   ll="eza -l --icons": Full long listing with icons.
        
    *   llm='eza -lbGd --git --sort=modified': List by modified date.
        
    *   la="eza -la --icons": List all files, including hidden ones.
        
    *   lx='eza -lbhHigUmuSa@ --time-style=long-iso --git --color-scale': My ultimate verbose listing, showing everything from permissions to Git status, with a time-style and color scale.

## A Little Extra: Keeping an Eye with bottom

As a final goodie, I want to showcase a tool that perfectly complements a well-tuned terminal setup: `bottom`. If you're like me and love to have a quick, comprehensive overview of your system's performance directly in your terminal, `bottom` is a fantastic choice.

### Why bottom?

[**Bottom**](https://github.com/ClementTsang/bottom) is a cross-platform graphical process and system monitor that's both powerful and highly customizable. It presents information clearly with graphs and easy-to-read summaries, making it simple to check CPU, memory, disk, and network usage at a glance. It's fast, resource-efficient, and, fitting with the theme of this post, can be configured to look just the way you like it.
![Test](images/ghostty_btm.png)
