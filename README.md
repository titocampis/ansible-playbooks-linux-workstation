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

## Sensitive Data managed by Ansible vault
To store the Ansible Sensitive Data we use [Ansible vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html).

> :paperclip: **NOTE:** If you have your ubuntu user on `/etc/sudoers` file to become sudo without password, you can just execute the ansible playbook with this flag and you can forget about ansible_vault:
> ```bash
> --extra-vars ansible_user=<your_ubuntu_user>
> ```

To create the vault.yml file:
```bash
ansible-vault create vault.yml
```

It will ask for password, and then **vi** editor will open and we need to fulfill it in yml format like this:

```yml
ansible_user: ''
ansible_become_pass: ''
```

Then, Ansible will create a vault file in the folder you executed the `ansible-vault`: `vault.yml`

To use the vault variables inside the playbooks, we need to include:

```yml
vars_files:
 - relative/path/from/playbook/to/vault/file
```

And then add at the end of the `ansible-playbook` execution `ask-vault-pass`:

```bash
ansible-playbook playbook... -i .... --ask-vault-pass
```

> :paperclip: **NOTE:** If we want to edit the vault file:
> ```bash
> ansible-vault edit vault.yml
> ```

## Launching wsl base ansible playbook

#### Ensure base packages installed on wsl (ubuntu):
```bash
ansible-playbook playbooks/wsl/base.yml -i inventories/wsl.ini --ask-vault-pass --tags base-packages --check
```

#### Configure useful topics on your favourite shell:

:one: Configure your favorite shell on the playbook the var `base_shell: <your_favourite_shell>` (by default it is `base_shell: '.bashrc'`)

:two: Launch the playbook

```bash
ansible-playbook playbooks/base.yml -i inventories/inventory.ini --ask-vault-pass --tags base-shell-config --check
```

#### Configure ohmyzsh and ~/.zshrc
We don't have ansible playbook, sorry. We think with this documentation it will be really straight-forward: [README_ohmyzsh.md](README_ohmyzsh.md)

#### Configure vim:
```bash
ansible-playbook playbooks/wsl/base.yml -i inventories/wsl.ini --ask-vault-pass --tags base-vim-config --check
```

#### More
To check more available tasks check [roles/base/tasks/main.yml](roles/base/tasks/main.yml)

## Launching wsl config-services playbook

#### Install and start docker on wsl (ubuntu):

:one: Configure on your playbook the var `docker_enabled: true`

:two: Launch the playbook
```bash
ansible-playbook playbooks/wsl/config-services.yml -i inventories/wsl.ini --ask-vault-pass --tags config-services-docker --check
```

#### Install terraform on wsl (ubuntu):

:one: Configure on your playbook the var `terraform_enabled: true`

:two: Launch the playbook
```bash
ansible-playbook playbooks/wsl/config-services.yml -i inventories/wsl.ini --ask-vault-pass --tags config-services-terraform --check
```

#### More
To check more available tasks check [roles/config-services/tasks/main.yml](roles/config-services/tasks/main.yml)
