# Ansible Playbooks to configure Linux Workstation :tophat:

Welcome to the Linux Workstation Configuration with Ansible! This repository is my go-to resource for effortlessly configuring a Linux Workstation using the power of Ansible. :tophat:

For now, the only workstation distro available is `Ubuntu`.

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

## Project Structure
```bash
inventories/ # Folder containing all the servers where ansible will run and its configuration.
    └── localhost.ini # Inventory with localhost to run the configuration in local machine.
plays/ # Folder containing all the playbooks ro be executed on the hosts, we have one playbook per role.
    ├── linux_workstation.yaml # Playbook which executes the base role (basic configuration for the server).
    └── ...
roles/ # Folder containing all the ansible roles (tasks to be executed on the playbooks).
    └──  base/ # Tasks for basic configuration of the server (packages, pubkeys, etc.).
          ├── defaults/main.yaml # Default configuration for the role.
          ├── tasks/
                ├── base_packages.yaml # Task to ensure the base packages installed.
                ├── main.yaml # File containing the configuration for all the tasks and how to use them.
                └──  ...
          └── ...
.ansible-lint # File to exclude warnings/errors when ansible-lint.
.gitignore # File including all the files and folder to not push into git.
.pre-commit-config.yaml # File to run hooks to check code when git commit.
general_vars.yaml # File for generic vars in all the playbook.
README_ohmyzsh.md # Documentation of how to install and configure ohmyzsh.
README.md # Repository documentation.
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

> [!TIP]
> If you have your ubuntu user on `/etc/sudoers` file to become sudo without password, you can skip the usage of `ansible-vault`.

To create the vault.yaml file:
```bash
ansible-vault create vault.yaml
```

It will ask for password, and then **vi** editor will open and we need to fulfill it in yaml format like this:
```yaml
ansible_become_pass: ""
```

Then, Ansible will create a vault file in the folder you executed the `ansible-vault`: `vault.yaml`


To use the vault variables inside the playbooks, we need to:

- Include the vault into the playbook confi:
```yaml
vars_files:
 - relative/path/from/playbook/to/vault/file
```

- Create a new file `vault_password.txt` and fulfill it just with the content of the vault password.

- Add to the `ansible-playbook` execution `--vault-password-file=vault_password.txt`:

```bash
ansible-playbook playbook... -i .... --vault-password-file=vault_password.txt
```

> [!NOTE]
> If we want to edit the vault file:
> ```bash
> ansible-vault edit vault.yaml --vault-password-file=vault_password.txt
> ```

## Launching base ansible playbook

#### Execute the full role
Tags: `base`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base --check
```

#### Ensure base packages installed
Tags: `base-packages`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-packages --check
```

#### Configure useful topics on your favourite shell
1. Configure your favorite shell on the playbook the var `base_shell: <your_favourite_shell>` (by default it is `base_shell: '.zshrc'`)
2. Launch the playbook:

Tags: `base-shell-config`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-shell-config --check
```

#### Configure ohmyzsh and ~/.zshrc
We don't have ansible playbook, sorry. We think with this documentation it will be really straight-forward: [README_ohmyzsh.md](README_ohmyzsh.md)

#### Configure vim
Tags: `base-vim-config`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-vim-config --check
```

#### Ensure and configure tmux
Tags: `base-tmux`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-tmux --check
```

#### Ensure docker installed, configured, enabled and started
1. Configure on your playbook the var `base_docker_enabled: true`
2. Launch the playbook:

Tags: `base-docker`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-docker --check
```

#### Ensure and configure terraform
1. Configure on your playbook the var `base_terraform_enabled: true`
2. Launch the playbook

Tags: `base-terraform`
```bash
ansible-playbook playbooks/linux_workstation.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags base-terraform --check
```

#### More
To check more available tasks check [roles/base/tasks/main.yaml](roles/base/tasks/main.yaml)
