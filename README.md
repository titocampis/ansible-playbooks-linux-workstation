# Ansible Playbooks to configure WSL (Ubuntu)

Repository containing all the playbooks to configure WSL (Ubuntu) using **Ansible**

## Install and configure Ubuntu in WSL

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

## Project Structure

```bash
inventories/ # Folder containing all the servers where ansible will run and its configuration
    └── wsl.ini # Inventory with localhost to run the configuration of the wsl (ubuntu)
plays/ # Folder containing all the playbooks ro be executed on the hosts, we have one playbook per role
    ├── base.yml # Playbook which executes the base role (basic configuration for the server)
    ├── ...
    └── 
roles/ # Folder containing all the ansible roles (tasks to be executed on the playbooks)
    ├── base/ # Tasks for basic configuration of the server (packages, pubkeys, etc.)
          ├── defaults/main.yml # Default configuration for the role
          ├── tasks/
                ├── base_packages.yml # Task to ensure the base packages installed
                ├── main.yaml # File containing the configuration for all the tasks and how to use them
                └──  ...
          └──  
    ├── config-services/ # Tasks for services configuration (docker, motd, sshd, etc.)
    └──  ...
.gitignore # File including all the files and folder to not push into git
README.md # Repository documentation
```

## Previous Steps before executing Ansible Playbooks

:one: Install python
```bash
sudo apt install python3
```

:two: Install python3-pip
```bash
pip3 install python3-pip
```

:four: Install ansible
```bash
sudo apt install ansible
```

## Launching wsl base ansible playbook

- To ensure base packages installed on wsl (ubuntu):
```bash
ansible-playbook playbooks/wsl/base.yml -i inventories/wsl.ini --ask-vault-pass --tags base-packages --check
```

- To configure useful topics on your favourite shell:
```bash
ansible-playbook playbooks/base.yml -i inventories/inventory.ini --ask-vault-pass --tags base-shell-config --check
```

- To configure ohmyzsh and ~/.zshrc... We don't have ansible playbook, sorry. We think with this documentation it will be really straight-forward: [README_ohmyzsh.md](README_ohmyzsh.md)

- To configure vim:
```bash
ansible-playbook playbooks/wsl/base.yml -i inventories/wsl.ini --ask-vault-pass --tags base-vim-config --check
```

To check more available tasks check [roles/base/tasks/main.yml](roles/base/tasks/main.yml)

## Launching wsl config-services playbook

- To install and start docker on wsl (ubuntu):
```bash
ansible-playbook playbooks/wsl/config-services.yml -i inventories/wsl.ini --ask-vault-pass --tags config-services-docker --check
```

To check more available tasks check [roles/config-services/tasks/main.yml](roles/config-services/tasks/main.yml)
