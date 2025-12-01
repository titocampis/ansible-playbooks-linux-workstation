## Index
1. [Install and configure Ubuntu in WSL](#install-and-configure-ubuntu-in-wsl)
2. [Project Structure](#project-structure)
3. [Previous Steps before executing Ansible Playbooks](#previous-steps-before-executing-ansible-playbooks)
4. [Sensitive Data managed by Ansible vault](#sensitive-data-managed-by-ansible-vault)
5. [Launching base ansible playbook](#launching-base-ansible-playbook)
    - [Ensure base packages installed](#ensure-base-packages-installed)
    - [Configure useful topics on your favourite shell](#configure-useful-topics-on-your-favourite-shell)
    - [Configure ohmyzsh and ~/.zshrc](#configure-ohmyzsh-and-zshrc)
    - [Configure vim](#configure-vim)
    - [Ensure and configure tmux](#ensure-and-configure-tmux)
    - [Ensure docker installed, configured, enabled and started](#ensure-docker-installed-configured-enabled-and-started)
    - [Ensure and configure terraform](#ensure-and-configure-terraform)
    - [More](#more)

## Install and configure Ubuntu in WSL

One of the more spread workstations is WSL (Windows Subsystem for Linux) that's why how to install and make first configurations is explained:

:one: Go to windows Search and look for `Turn Windows features windows on or off`.

:two: Check that `Windows Subsystem for Linux` is enabled

:three: Open Powershell as administrator

:four: Execute the following command
```ps1
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

:five: Enable Virtual Machine feature
```ps1
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

:six: Download the Linux kernel update package
```ps1
wsl.exe --install
```

or

```ps1
wsl.exe --upgdate
```

:seven: Set WSL 2 as your default version
```ps1
wsl --set-default-version 2
```

:eight: Go to the microsoft store and download `Ubuntu`

:nine: Once it is downloaded, open it and enjoy!!!


#