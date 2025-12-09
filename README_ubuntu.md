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

## Keyboard
Less and greater keys are on:
- Less: `Alt Gr + Shit + z`
- Greater: `Alt Gr + Shit + x`

## KVM
### Installation
:one: Run the kvm task

:two: Start downloading Ubuntu ISO:
```bash
wget https://releases.ubuntu.com/24.04/ubuntu-24.04.3-desktop-amd64.iso \
    -O /mnt/kvm/isos/ubuntu-24.04.3-desktop-amd64.iso
```

:three: Change permissions of the iso
```bash
sudo chown libvirt-qemu:kvm /mnt/kvm/isos/ubuntu-24.04.3-live-server-amd64.iso
sudo chmod 644 /mnt/kvm/isos/ubuntu-24.04.3-live-server-amd64.iso
```

:four: Run the `virt-manager`:
```bash
sudo virt-manager
```

:five: Create a new vm from and configure it:
- ram 
- vcpus
- disk

> We can do it manually but didn't work for me --> I think it is something related with the GUI installer
> ---
> :four: Create the virtual disk image file for the VM hard drive `20G`
> ```bash
> sudo qemu-img create -f qcow2 /mnt/kvm/ubuntu24_vm.qcow2 20G
> ```
> 
> :five: Change permissions of the qcow2 file:
> ```bash
> sudo chown libvirt-qemu:kvm /mnt/kvm/ubuntu24_vm.qcow2
> sudo chmod 660 /mnt/kvm/ubuntu24_vm.qcow2
> ```
> 
> :six: Create the vm:
> ```bash
> sudo virt-install \
> --name ubuntu24-vm \
> --ram 4096 \
> --vcpus 2 \
> --cpu host \
> --os-variant ubuntu24.04 \
> --cdrom /mnt/kvm/isos/ubuntu-24.04.3-desktop-amd64.iso \
> --disk path=/mnt/kvm/ubuntu24_vm.qcow2,size=20 \
> --network network=default \
> --graphics spice \
> --boot cdrom,hd
> ```
> 
> - `--name`: VM name.
> - `--ram` and `--vcpus`: resources.
> - `--cpu host`: exposes host CPU features.
> - `--os-variant`: helps with optimization.
> - `--cdrom`: ISO for installation.
> - `--disk`: specify the disk.
> - `--network`: default NAT network.
> - `--graphics spice`: GUI console.
> - `--boot cdrom,hd`: boot from ISO first, then disk.
 
### Manage VM's

#### General
`virt-manager`: visual manager of ISOS
`kvm -> virsh`: real manager
`virsh list --all`: see all the running vms
`virsh edit <vm_name>`: change the configuration of the vm
`virsh dumpxml <vm_name>`: check xml configuration without editing 
`virsh start <vm_name>`: start the vm
`virsh shutdown <vm_name>`: gracefully shutdown the vm
`virsh reboot <vm_name>`: gracefully reboot the vm
`virsh destroy <vm_name>`: to force stop the vm

#### Resize the vm disk
:one: Check the qcow2 file
```bash
sudo virsh dumpxml <vm_name> | grep qcow2
```
or
```bash
qemu-img info /var/lib/libvirt/images/ubuntu24-vm.qcow2
```

It should be something like: `/var/lib/libvirt/images/ubuntu24-vm.qcow2`

:two: Resize it:
```bash
qemu-img resize /var/lib/libvirt/images/ubuntu24-vm.qcow2
```

:three: Check the new size:
```bash
qemu-img info /var/lib/libvirt/images/ubuntu24-vm.qcow2
```

:four: Inside the vm --> resize the main partition


## WIP
- Think about vim=nvim with aliases or install vim-gtk to have clipboard systemwide
