# Documentation of how to install and configure ohmyzsh in WSL (Ubuntu)

## Installation

The official documentation: [https://ohmyz.sh/](https://ohmyz.sh/)

My ansible playbook: [roles/base/tasks/ohmyzsh.yml](roles/base/tasks/ohmyzsh.yml)

## Configuration

:one: Open the `~/.zshrc` file and include
```bash
# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="agnoster"

## set colors for LS_COLORS
eval `dircolors ~/.dircolors`

...

# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    history
        sudo
        web-search
        copypath
        copyfile
)

source $ZSH/oh-my-zsh.sh
```