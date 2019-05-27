---
title: "My Dotfiles - Part 5: Visual Studio Code"
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

## What's VSCode
VSCode or Visual Studio Code is the open source code-editor from Microsoft. Like some of the alternative open-source editors (Atom) it is based on electron to enable support on multiple platforms. What makes Visual Studio Code great is: 
1. Extensibility
2. Open Source
3. IntelliSense Code Completion
4. Cross Platform
5. Pretty fast \\

Different to its "bigger brother" Visual Studio VSCode isn't an IDE, however it is more powerful than an regular code-editor. It is between being a full-blown IDE with Debugging Support, ... and a Code Editor. VSCode offers support for debugging and in my opinion has a great UI. VSCode also has git integration which can be extended by installing extensions. However the most important thing in my opinion is the community. VSCode was selected on the StackOverflow Developer Survey as the most used code-editor. It has a huge amount of extensions and themes. 
### Extensions
I believe that extensions are the heart of VSCode. With them you can configure the theme and behavior of your editor. For example you can add snippets, keybinding or even support for entire languages. Luckily VSCode has a huge amount of extensions and you cann install all of them by simply heading to the extensions section. 
### Further Resources
Of Cause I did not cover all the features of VSCode. To learn more read some of these other articles:
- [Official VSCode Webpage](https://code.visualstudio.com/)
- [Traversy Media's Intro to VSCode (Web Developement)](https://www.youtube.com/watch?v=fnPhJHN0jTE)
- [Python Development Setup from realpython.com](https://realpython.com/python-development-visual-studio-code/)
- If you want to get started with the language of your choice just google: *LANGUAGE_NAME vscode*

## How we install and configure VSCode
To install VSCode I have chosen to have the following structure: 
```
dotfiles
├── vscode
│   ├── install.sh (The installation file)
│   ├── settings.json 
│   ├── keybindings.json 
│   └── extensions.txt (list of extensions)
├── distro.sh
├── packages.sh
├── helpers.sh
├── special_installs.sh
└── install.sh
```
In the `vscode/install.sh` we will put the following:
```bash
#!/bin/bash
exho_info "Installing VSCode"
_install code

while read EXTENSION
do
    echo_info "Install VSCode Extension: $EXTENSION"
    code --install-extension $EXTENSION
done < $HOME/.dotfiles/vscode/extensions.txt 

ln -sf $HOME/.dotfiles/vscode/settings.json $HOME/.config/Code\ -\ OSS/User/settings.json
ln -sf $HOME/.dotfiles/vscode/keybindings.json $HOME/.config/Code\ -\ OSS/User/keybindings.json
```
The `extensions.txt` will simply contain a list of extensions. For example:
```
peterjausovec.vscode-docker
vscodevim.vim
...
```
## My Configuration
My VSCode configuration is made for python development. The main features are vim keybindings, python support and, at least in my opinion, nice aesthetics.
### Extensions
The Extensions I use are:
- **autoDocstring from Nils Werner**: Generate a docstring for python functions
- **Code Spell Checker from Street Side Software**: The clue is in the name...
- **DotENV from mikestead**: Support for .env files
- **GitLens from Eric Amodio**: Advanced Git options
- **indent-rainbow from oderwat**: See indentation Level
- **Live Share from Microsoft**: Share your code and edit code together
- **Material Icon Theme from Phillip Kief**: Nice icon Theme
- **Python from Microsoft**: Python Support
- **Rainbow Brackets from 2gua**: See matching brackets
- **Vim from vscodevim**: Vim Support
- **Visual Studio IntelliCode**: Better code completion
- **Yaml from Red Hat**: YAML file support
- **Dracula Official from Dracula Theme**: I don't use this directly as I already use a custom pywal theme however the color scheme is Dracula  

### Settings
The `settings.json` file is as follows:
```json
{
    "editor.fontLigatures": true,
    "editor.fontFamily": "FuraCode Nerd Font",
    "breadcrumbs.enabled": true,
    "editor.fontSize": 16,
    "editor.formatOnSave": true,
    "editor.lineNumbers": "relative",
    "editor.minimap.maxColumn": 60,
    "editor.minimap.renderCharacters": false,
    "explorer.openEditors.visible": 0,
    "files.autoSave": "onFocusChange",
    "files.exclude": {
        "**/.git": true,
        "**/.svn": true,
        "**/.hg": true,
        "**/CVS": true,
        "**/.DS_Store": true,
        "**/.mypy_cache": true,
        "**/.vscode": true,
        "**/__pycache__": true
    },
    "gitlens.advanced.messages": {
        "suppressShowKeyBindingsNotice": true
    },
    "gitlens.codeLens.enabled": false,
    "gitlens.hovers.currentLine.enabled": false,
    "indentRainbow.colors": [
        "rgba(20,20,20,0.3)",
        "rgba(50,50,50,0.3)",
        "rgba(80,80,80,0.3)",
        "rgba(110,110,110,0.3)",
        "rgba(140,140,140,0.3)"
    ],
    "indentRainbow.errorColor": "rgba(128,32,32,0)",
    "python.formatting.autopep8Args": [
        "--indent-size=4",
        "--ignore=E501,C0330"
    ],
    "python.formatting.blackArgs": [
        "--line-length=100"
    ],
    "python.formatting.provider": "black",
    "python.linting.flake8Enabled": false,
    "python.linting.mypyEnabled": true,
    "python.linting.pylintEnabled": true,
    "search.exclude": {
        "**/__pycache": true,
        "**/__pycache__": true
    },
    "workbench.iconTheme": "material-icon-theme",
    "workbench.settings.editor": "json",
    "workbench.statusBar.feedback.visible": false,
    "python.jediEnabled": false,
    "vim.easymotion": true,
    "vim.sneak": true,
    "vim.incsearch": true,
    "vim.useCtrlKeys": true,
    "vim.hlsearch": true,
    "vim.insertModeKeyBindings": [
        {
            "before": [
                "j",
                "j"
            ],
            "after": [
                "<Esc>"
            ]
        }
    ],
    "vim.normalModeKeyBindingsNonRecursive": [
        {
            "before": [
                "<leader>",
                "d"
            ],
            "after": [
                "d",
                "d"
            ]
        },
        {
            "before": [
                "<C-n>"
            ],
            "commands": [
                ":nohl"
            ]
        },
        {
            "before": [
                "y"
            ],
            "after": [
                "\"",
                "*",
                "y"
            ]
        },
        {
            "before": [
                "p"
            ],
            "after": [
                "\"",
                "*",
                "p"
            ]
        }
    ],
    "vim.leader": "<space>",
    "vim.handleKeys": {
        "<C-a>": false,
        "<C-f>": false
    },
    "vim.visualModeKeyBindingsNonRecursive": [
        {
            "before": [
                "y"
            ],
            "after": [
                "\"",
                "*",
                "y"
            ]
        },
        {
            "before": [
                "<tab>"
            ],
            "after": [
                ">"
            ]
        },
        {
            "before": [
                "<s-tab>"
            ],
            "after": [
                "<"
            ]
        }
    ],
    "extensions.ignoreRecommendations": true,
    "gitlens.hovers.currentLine.over": "line",
    "gitlens.mode.statusBar.enabled": false,
    "material-icon-theme.activeIconPack": "angular",
    "material-icon-theme.hidesExplorerArrows": false,
    "material-icon-theme.showWelcomeMessage": false,
    "autoDocstring.docstringFormat": "default",
    "terminal.external.linuxExec": "st",
    "terminal.integrated.shell.linux": "/usr/bin/bash",
    "python.linting.pylintArgs": [
        "--extension-pkg-whitelist=numpy,tensorflow"
    ],
    "editor.selectionClipboard": false,
    "terminal.integrated.fontFamily": "FuraCode Nerd Font",
    "editor.suggestSelection": "first",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "workbench.settings.useSplitJSON": true,
    "window.titleBarStyle": "custom",
    "workbench.colorTheme": "vs-wal"
}
```
### Keybindings
My Keybindings are as follows: 
```json
// Place your key bindings in this file to override the defaults
[
    {
        "key": "f1",
        "command": "python.execInTerminal",
    },
    {
        "key": "ctrl+alt+s",
        "command": "python.sortImports"
    },
    {
        "key": "ctrl+alt+u",
        "command": "editor.action.transformToUppercase"
    },
    {
        "key": "ctrl+alt+l",
        "command": "editor.action.transformToLowercase"
    },
    {
        "key": "ctrl+space",
        "command": "python.execSelectionInTerminal",
        "when": "editorFocus && editorHasSelection && editorLangId == 'python'"
    },
    {
        "key": "ctrl+enter",
        "command": "-python.execSelectionInTerminal",
        "when": "editorFocus && editorHasSelection && editorLangId == 'python'"
    },
    {
        "key": "ctrl+shift+r",
        "command": "workbench.action.reloadWindow"
    },
    {
        "key": "ctrl+alt+right",
        "command": "workbench.action.splitEditor"
    },
    {
        "key": "ctrl+shift+alt+cmd+7",
        "command": "-workbench.action.splitEditor"
    },
]
```

To conclude I believe VSCode is the best Code Editor there is at the moment as it has many features of an IDE. I really recommend that you give it a try, as almost all languages are supported and it can drastically improve your productivity as a developer. 