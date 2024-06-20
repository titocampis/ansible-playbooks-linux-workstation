# Ansible Playbooks to configure WSL (Ubuntu) :tophat:

Welcome to the WSL Ubuntu Configuration with Ansible! This repository is my go-to resource for effortlessly configuring WSL (Windows Subsystem for Linux) using the power of Ansible. :tophat:

## Index

1. [Install and configure Ubuntu in WSL](#install-and-configure-ubuntu-in-wsl)
2. [Project Structure](#project-structure)
3. [Previous Steps before executing Ansible Playbooks](#previous-steps-before-executing-ansible-playbooks)
4. [Sensitive Data managed by Ansible vault](#sensitive-data-managed-by-ansible-vault)
5. [Launching wsl base ansible playbook](#launching-wsl-base-ansible-playbook)
    - [Ensure base packages installed on wsl (ubuntu)](#ensure-base-packages-installed-on-wsl-ubuntu)
    - [Configure useful topics on your favourite shell](#configure-useful-topics-on-your-favourite-shell)
    - [Configure ohmyzsh and ~/.zshrc](#configure-ohmyzsh-and-zshrc)
    - [Configure vim](#configure-vim)
    - [More](#more)
6. [Launching wsl config-services playbook](#launching-wsl-config-services-playbook)
    - [Install and start docker on wsl (ubuntu)](#install-and-start-docker-on-wsl-ubuntu)
    - [Install terraform on wsl (ubuntu)](#install-terraform-on-wsl-ubuntu)
    - [More](#more-1)

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
    ├── base.yaml # Playbook which executes the base role (basic configuration for the server)
    ├── ...
    └── 
roles/ # Folder containing all the ansible roles (tasks to be executed on the playbooks)
    ├── base/ # Tasks for basic configuration of the server (packages, pubkeys, etc.)
          ├── defaults/main.yaml # Default configuration for the role
          ├── tasks/
                ├── base_packages.yaml # Task to ensure the base packages installed
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

## Sensitive Data managed by Ansible vault
To store the Ansible Sensitive Data we use [Ansible vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html).

> :paperclip: **NOTE:** If you have your ubuntu user on `/etc/sudoers` file to become sudo without password, you can just execute the ansible playbook with this flag and you can forget about ansible_vault:
> ```bash
> --extra-vars ansible_user=<your_ubuntu_user>
> ```

To create the vault.yaml file:
```bash
ansible-vault create vault.yaml
```

It will ask for password, and then **vi** editor will open and we need to fulfill it in YAML format like this:

```yaml
ansible_user: ''
ansible_become_pass: ''
```

Then, Ansible will create a vault file in the folder you executed the `ansible-vault`: `vault.yaml`

To use the vault variables inside the playbooks, we need to include:

```.yaml
vars_files:
 - relative/path/from/playbook/to/vault/file
```

And then add at the end of the `ansible-playbook` execution `ask-vault-pass`:

```bash
ansible-playbook playbook... -i .... --ask-vault-pass
```

> :paperclip: **NOTE:** If we want to edit the vault file:
> ```bash
> ansible-vault edit vault.yaml
> ```

## Launching wsl base ansible playbook
#### Ensure base packages installed on wsl (ubuntu)
```bash
ansible-playbook playbooks/wsl/base.yaml -i inventories/wsl.ini --ask-vault-pass --tags base-packages --check
```

#### Configure useful topics on your favourite shell

1. Configure your favorite shell on the playbook the var `base_shell: <your_favourite_shell>` (by default it is `base_shell: '.bashrc'`)
2. Launch the playbook:
```bash
ansible-playbook playbooks/base.yaml -i inventories/inventory.ini --ask-vault-pass --tags base-shell-config --check
```

#### Configure ohmyzsh and ~/.zshrc
We don't have ansible playbook, sorry. We think with this documentation it will be really straight-forward: [README_ohmyzsh.md](README_ohmyzsh.md)

#### Configure vim
```bash
ansible-playbook playbooks/wsl/base.yaml -i inventories/wsl.ini --ask-vault-pass --tags base-vim-config --check
```

#### More
To check more available tasks check [roles/base/tasks/main.yaml](roles/base/tasks/main.yaml)


## Launching wsl config-services playbook
#### Install and start docker on wsl (ubuntu)

1. Configure on your playbook the var `docker_enabled: true`
2. Launch the playbook:
```bash
ansible-playbook playbooks/wsl/config-services.yaml -i inventories/wsl.ini --ask-vault-pass --tags config-services-docker --check
```

#### Install terraform on wsl (ubuntu)

1. Configure on your playbook the var `terraform_enabled: true`
2. Launch the playbook
```bash
ansible-playbook playbooks/wsl/config-services.yaml -i inventories/wsl.ini --ask-vault-pass --tags config-services-terraform --check
```

#### More
To check more available tasks check [roles/config-services/tasks/main.yaml](roles/config-services/tasks/main.yaml)
