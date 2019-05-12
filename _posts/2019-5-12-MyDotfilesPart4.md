---
title: "My Dotfiles - Part 4: ZSH"
author: Peer Rheinboldt
categories:
  - Blog
tags:
  - Dotfiles
  - Dev. Environment
---

**Warning:** As I'm creating my [dotfiles](https://github.com/peerlator/dotfiles-new) while writing this series, the dotfiles aren't completely finished. So please don't install them yet  
{: .notice--warning}

## What's this?
In this blog post series I will tell you about how I created my dotfiles and how you can create yours. I will tell you about what I use, how I use and configure these programs and how I have made changing the configurations easy. The structure of the following blogposts will be:
{% include dotfiles-table-of-contents.md %}

## What's ZSH
ZSH (short for Z shell) is an version of sh (Bourne Shell), just like bash and fish. ZSH has in comparison to sh many more features like support for plugins and themes. It also has built-in features like recursive path expansion (/u/lo/b expands to /usr/local/bin when pressing *tab*) or automatic cd (Simply type in the name of the directory and cd into it automatically).

To manage the plugins and themes so called frameworks are available. I chose [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/), as I believe it has the best feature set and a huge community (85,000+ stars on github). Oh-My-Zsh features 200+ plugins, 140+ themes and an auto-update tool. However this is only the built-in stuff. Additionally you can install many plugins and themes from the corresponding git repository. See the [oh-my-zsh wiki](https://github.com/robbyrussell/oh-my-zsh/wiki) for an overview of the available themes and plugins. 

## How we install and configure ZSH
ZSH has its config file located under `~/.zshrc`. However I find it very inconvenient to have all the config in 1 file as it becomes not navigable, when you have lots of configurations. Luckily we don't have to merge our config files, like we did with polybar and i3wm. With ZSH you can use source to simply execute/load a different file (**NOTE:** The `. <filename>` used in `install.sh` is short for `source <filename>`).
With this command, we can simply symlink a `zshrc` file to `~/.zshrc` and in this file source all the other `.zsh` files in our dotfiles. Our structure will be as follows:
```
dotfiles
├── zsh
│   ├── install.sh (The installation file)
│   ├── zshrc
│   ├── prompt.zsh (theme settings)
│   ├── sourcing.zsh (The sourcing of zsh files)
│   └── startups.zsh (programs to start and variables)
├── distro.sh
├── packages.sh
├── helpers.sh
├── special_installs.sh
└── install.sh
```
**Info:** If you don't know what the files do please read part 2 and part 3 of this series  
{: .notice}

In the `zsh/install.sh` file we will put the following:
```shell
_install zsh

# oh-my-zsh
git clone https://github.com/robbyrussell/oh-my-zsh.git $HOME/.oh-my-zsh

# change shell
chsh zsh

# plugins and theme
git clone https://github.com/zsh-users/zsh-autosuggestions $HOME/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone https://github.com/bhilburn/powerlevel9k.git $HOME/.oh-my-zsh/custom/themes/powerlevel9k
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-completions $HOME/.oh-my-zsh/custom/plugins/zsh-completions

# link zshrc file
ln -sf $HOME/.dotfiles/zsh/zshrc $HOME/.zshrc
```

## Basics of ZSH's Configuration
The configuration of ZSH is luckily fairly simple. Most of the settings can be set through variables:
```zsh
export <NAME>=<VALUE>
<NAME>=<VALUE>

Examples:
  export PATH=$PATH:$HOME/.dotfiles/scripts
  POWERLEVEL9K_PYENV_PROMPT_ALWAYS_SHOW=true
``` 
If you want to improve your productivity an extremely helpful tool are aliases. With aliases you can alias a phrase to a command:
```zsh 
alias <phrase>=<command>

Examples:
  alias ls="lsd"
  alias gcm="git commit -m $1" # $1 is the shell argument
```
These are already the basics. With this you can almost completely understand my ZSH configuration.

## My ZSH config
![](/assets/images/my-zsh.png)
*This is what my ZSH config looks like*
In my config I use [powerlevel9k](https://github.com/bhilburn/powerlevel9k) as my theme and multiple plugins: Autocompletion, syntax highlighting and a better completion plugin. My configuration is as follows:
```zsh
# zshrc
export DOTFILES=~/.dotfiles

export PROJECTS=~/Code

# start window manager
if [[ ! $DISPLAY && $XDG_VNTR -eq 1 ]]; then
	exec startx
fi
# source all the zsh files
source $HOME/.dotfiles/zsh/sourcing.zsh

# Fancy system info
neofetch
```

```zsh
# sourcing.zsh
if [[ -a ~/.localrc ]] # local config
then
	source ~/.localrc
fi

typeset -U config_files
config_files=($HOME/.dotfiles/**/*.zsh)

# load pyth files
for file in ${(M)config_files:#*/path.zsh}
do
	source $file
done

# load every zsh file, except for python, path, completion and this file (to not have infinite loop)
for file in ${${${${config_files:#*/path.zsh}:#*/completion.zsh}:#*/py.zsh}:#*/sourcing.zsh}
do
	source $file
done

source $HOME/.dotfiles/python/py.zsh

unset config_files
```

```zsh
# startups.zsh
ZSH_THEME="powerlevel9k/powerlevel9k"
plugins=(
    zsh-autosuggestions
    git
    zsh-syntax-highlighting
    zsh-completions
)
export PATH=$PATH:$HOME/.dotfiles/scripts
export ZSH=$HOME/.oh-my-zsh
export EDITOR="st -e nvim"
source $ZSH/oh-my-zsh.sh
autoload -U compinit && compinit
export LANG="en_US.UTF-8"
```

```zsh
# prompt.zsh
POWERLEVEL9K_PYENV_PROMPT_ALWAYS_SHOW=true

POWERLEVEL9K_MODE='awesome-patched'
POWERLEVEL9K_MODE='nerdfont-complete'

POWERLEVEL9K_PROMPT_ON_NEWLINE=true
POWERLEVEL9K_PROMPT_ADD_NEWLINE=true

POWERLEVEL9K_SHORTEN_DIR_LENGTH=2
POWERLEVEL9K_SHORTEN_DELIMITER=""
POWERLEVEL9K_SHORTEN_STRATEGY="truncate_beginning"

POWERLEVEL9K_MULTILINE_FIRST_PROMPT_PREFIX="%F{blue}\u256D\u2500%f"
POWERLEVEL9K_MULTILINE_LAST_PROMPT_PREFIX="%F{blue}\u2570\uf460%f"

POWERLEVEL9K_COMMAND_EXECUTION_TIME_THRESHOLD=0

POWERLEVEL9K_OS_ICON_BACKGROUND="back"
POWERLEVEL9K_OS_ICON_FOREGROUND="aqua"


POWERLEVEL9K_DIR_DEFAULT_FOREGROUND="black"
POWERLEVEL9K_DIR_HOME_SUBFOLDER_FOREGROUND="black"
POWERLEVEL9K_DIR_ETC_FOREGROUND="black"
POWERLEVEL9K_DIR_HOME_FOREGROUND="black"
POWERLEVEL9K_DIR_DEFAULT_BACKGROUND="aqua"
POWERLEVEL9K_DIR_HOME_SUBFOLDER_BACKGROUND="aqua"
POWERLEVEL9K_DIR_ETC_BACKGROUND="aqua"
POWERLEVEL9K_DIR_HOME_BACKGROUND="aqua"

POWERLEVEL9K_TIME_FOREGROUND="white"
POWERLEVEL9K_TIME_BACKGROUND="black"

POWERLEVEL9K_DATE_FOREGROUND="white"
POWERLEVEL9K_DATE_BACKGROUND="black"

POWERLEVEL9K_PYENV_FOREGROUND="yellow"
POWERLEVEL9K_PYENV_BACKGROUND="black"

POWERLEVEL9K_ROOT_INDICATOR_BACKGROUND="black"

POWERLEVEL9K_COMMAND_EXECUTION_TIME_BACKGROUND='black'
POWERLEVEL9K_COMMAND_EXECUTION_TIME_FOREGROUND='blue'

POWERLEVEL9K_VCS_CLEAN_FOREGROUND='black'
POWERLEVEL9K_VCS_CLEAN_BACKGROUND='green'
POWERLEVEL9K_VCS_UNTRACKED_FOREGROUND='black'
POWERLEVEL9K_VCS_UNTRACKED_BACKGROUND='red'
POWERLEVEL9K_VCS_MODIFIED_FOREGROUND='black'
POWERLEVEL9K_VCS_MODIFIED_BACKGROUND='yellow'

POWERLEVEL9K_VCS_UNTRACKED_ICON='\u25CF'
POWERLEVEL9K_VCS_UNSTAGED_ICON='\u00b1'
POWERLEVEL9K_VCS_INCOMING_CHANGES_ICON='\u2193'
POWERLEVEL9K_VCS_OUTGOING_CHANGES_ICON='\u2191'
POWERLEVEL9K_VCS_COMMIT_ICON="\uf417"

POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(os_icon dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(command_execution_time status root_indicator time date_joined pyenv)

ZSH_THEME="powerlevel9k/powerlevel9k"
```

I hope, that this article has acted as something useful for you. I recommend that you go ahead and install ZSH on your own system. ZSH isn't only supported on Linux, but also on Mac. I personally love ZSH and prefer it over bash. In the next post of this Series I will write about another one of my favorite, if not my most favorite, tool: VS Code, the open source code editor from Microsoft.