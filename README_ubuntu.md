# README for Ubuntu Host Configuration

Read this document carefully if you are configuring Ubuntu in your host.

## Index

This index lists the main sections on this page for quick navigation:

- [README for Ubuntu Host Configuration](#readme-for-ubuntu-host-configuration)
  - [Index](#index)
  - [Other packages to install manually](#other-packages-to-install-manually)
    - [Google Chrome](#google-chrome)
    - [Brave](#brave)
    - [VSCode](#vscode)
    - [Bitwarden](#bitwarden)
  - [Oh-My-Zsh](#oh-my-zsh)
  - [Configure CopyQ keyboard shortcut](#configure-copyq-keyboard-shortcut)
  - [New keys on keyboard layout](#new-keys-on-keyboard-layout)
  - [WIP](#wip)

## Other packages to install manually

The following packages are really useful but I considered better to install them manually.

### Google Chrome
```bash
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo "deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main" | tee /etc/apt/sources.list.d/google-chrome.list
apt update
apt upgade
apt install google-chrome-stable
```

### Brave
https://brave.com/linux/
```bash
curl -fsS https://dl.brave.com/install.sh | sh
```

### VSCode
https://code.visualstudio.com/docs/setup/linux

:one: Go into `tmp` folder
```bash
cd /tmp/
```

:two: Download the binary specified on the web

:three: Install it
```bash
sudo apt install ./<file>.deb
```

:three: Automatically install the apt repository and signing key
```bash
echo "code code/add-microsoft-repo boolean true" | sudo debconf-set-selections
```

> You can do it manually:
> Add the Microsoft signing key
> ```bash
> wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
> sudo install -D -o root -g root -m 644 microsoft.gpg /usr/share/keyrings/microsoft.gpg
> rm -f microsoft.gpg
> ```
>
> Create the repository file manually on `/etc/apt/sources.list.d/vscode.sources`
> ```bash
> cat << EOF > /etc/apt/sources.list.d/vscode.sources
> Types: deb
> URIs: https://packages.microsoft.com/repos/code
> Suites: stable
> Components: main
> Architectures: amd64,arm64,armhf
> Signed-By: /usr/share/keyrings/microsoft.gpg
> EOF
> ```
>
> Update packages and install the package
> ```bash
> sudo apt install apt-transport-https
> sudo apt update
> sudo apt install code # or code-insiders
> ```

### Bitwarden
I prefer to use it via web.

## Oh-My-Zsh 
> :warning: **WARNING:** Machine needs to be rebooted or all terminal processes killed in order to apply changes. 
> 
> Remember to change the text font to a Nerd one in the Terminal Gnome Configuration.

To configure plugins add to `~/.zshrc`:
```bash
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
```

To download them:
Clone the `zsh-autosuggestions` in the folder `~/.oh-my-zsh/custom/plugins/`
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

Clone the `zsh-zsh-syntax-highlighting` in the folder `~/.oh-my-zsh/custom/plugins/`
```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
``` 
## Configure CopyQ keyboard shortcut

:one: Check the custom shortcuts configuration of your user:
```bash
gsettings get org.gnome.settings-daemon.plugins.media-keys custom-keybindings
```
> If there is some shortcut configured you should see something like this on the output:
> ```bash
> /org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/
> ```
> Or:
> ```bash
> /org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/custom0/
> ```

:two: Check the configuration of copyq is not configured:
```bash
gsettings list-recursively org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/custom0/
```
Or:
```bash
gsettings list-recursively org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/
```

> :paperclip: **Note:** You should see something like: 
> ```bash
> org.gnome.settings-daemon.plugins.media-keys.custom-keybinding binding '<Control>dead_grave'
> org.gnome.settings-daemon.plugins.media-keys.custom-keybinding command 'copyq show'
> org.gnome.settings-daemon.plugins.media-keys.custom-keybinding name 'CopyQ'
>```

:three: If the copy keyboard shortcut is not configured, create it:
```bash
gsettings set org.gnome.settings-daemon.plugins.media-keys custom-keybindings \
"['/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/']"
```

:four: Configure it:
```bash
gsettings set org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/ name 'CopyQ'
gsettings set org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/ command 'copyq show'
gsettings set org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/ binding '<Control>dead_grave'
```

:five: Check the configuration have been added:
```bash
gsettings get org.gnome.settings-daemon.plugins.media-keys custom-keybindings
```

:six: Check the configuration is well:
```bash
gsettings list-recursively org.gnome.settings-daemon.plugins.media-keys.custom-keybinding:/org/gnome/settings-daemon/plugins/media-keys/custom-keybindings/copyq/
```

## New keys on keyboard layout

> :warning: **WARNING:** This procedure can break your keyboard layout, be really careful

As my laptop does not have the keys '< >', I want to type them when:
- Alt Gr + G: >
- Alt Gr + L: <

:one: Check which `keycode` `G` and `L`have (any user):
```bash
xev
```

Type the key and see logs.

:two: Grep the keycode to see the key number (sudo):
```bash
sudo grep <keycode> /usr/share/X11/xkb/keycodes/evdev
```

:three: Copy the `es` layout into `es-custom`
```bash
sudo cp /usr/share/X11/xkb/symbols/es /usr/share/X11/xkb/symbols/es-custom
```

:four: Edit the `es-custom` layout and replace de `3rd and 4th columns` with the desired symbol:
```bash
sudo vim /usr/share/X11/xkb/symbols/es-custom
```
```bash
    // -------------------  Greater and less --------------------------------------------
    key <AC09>  { [ l, L, less, Less ] };
    key <AC05>  { [ g, G, greater, Greater ] };
    // -------------------  Greater and less --------------------------------------------
```

:five: Apply the new layout config and pray :pray:
```bash
sudo setxkbmap -layout es-custom
```

> :collision: **Note:** If something went wrong, roll-back
> ```
> sudo setxkbmap -layout es
> ```

## WIP
- Think about vim=nvim with aliases or install vim-gtk to have clipboard systemwide
