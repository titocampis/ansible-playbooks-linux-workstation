# Ansible Playbooks to configure Linux Workstation :tophat:

Welcome to the Linux Workstation Configuration with Ansible! This repository is my go-to resource for effortlessly configuring a Linux Workstation using the power of Ansible. :tophat:

For now, the only workstation distro available is `Ubuntu` for WSL or for a Linux host.

Read this document carefully if you are going to use it to configure your Linux Workstation. Read as well one of the following, depending of the platform you are using:
- **Ubuntu Host:** (README_ubuntu.md)[README_ubuntu.md]
- **WSL:** (README_wsl.md)[README_wsl.md]

## Index

This index lists the main sections on this page for quick navigation:

- [Ansible Playbooks to configure Linux Workstation :tophat:](#ansible-playbooks-to-configure-linux-workstation-tophat)
  - [Index](#index)
  - [Project Structure](#project-structure)
  - [Previous Steps before executing Ansible Playbooks](#previous-steps-before-executing-ansible-playbooks)
  - [Sensitive Data managed by Ansible vault](#sensitive-data-managed-by-ansible-vault)
  - [Launching base ansible playbook](#launching-base-ansible-playbook)
      - [Execute the full role](#execute-the-full-role)
      - [Ensure base packages installed](#ensure-base-packages-installed)
      - [Configure useful topics on your favourite shell](#configure-useful-topics-on-your-favourite-shell)
      - [More](#more)


## Project Structure

```bash
inventories/ # Folder containing all the servers where ansible will run and its configuration.
    └── localhost.ini # Inventory with localhost to run the configuration in local machine.
plays/ # Folder containing all the playbooks ro be executed on the hosts, we have one playbook per role.
    ├── ubuntu.yaml # Playbook which configures the basics for start enjoying ubuntu desktop.
    ├── wsl_ubuntu.yaml # Playbook which configures the basics for start enjoying ubuntu in wsl.
    └── ...
    roles/ # Folder containing all the ansible roles (tasks to be executed on the playbooks).
        └──  b4syk_ubuntu/ # Tasks for basic configuration of ubuntu server (packages, pubkeys, etc.).
            ├── defaults/main.yaml # Default configuration for the role.
            ├── tasks/
                    ├── b4syk_packages.yaml # Task to ensure the base packages installed.
                    ├── main.yaml # File containing the configuration for all the tasks and how to use them.
                    └──  ...
            └── ...
        └──  b4syk_wsl/ # Tasks for basic configuration of ubuntu in wsl (packages, pubkeys, etc.).
            ├── defaults/main.yaml # Default configuration for the role.
            ├── tasks/
                    ├── b4syk_packages.yaml # Task to ensure the base packages installed.
                    ├── main.yaml # File containing the configuration for all the tasks and how to use them.
                    └──  ...
            └── ...
.ansible-lint # File to exclude warnings/errors when ansible-lint.
.gitignore # File including all the files and folder to not push into git.
.pre-commit-config.yaml # File to run hooks to check code when git commit.
general_vars.yaml # File for generic vars in all the playbook.
README_wsl.md # Extra documentation for WSL configuration.
README_ubuntu.md # Extra documentation for Ubuntu configuration.
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

Create a new file `vault-password.txt` and fulfill it just with the content of the vault password.

To create the vault.yaml file:
```bash
ansible-vault create vault.yaml --vault-password-file=vault-password.txt
```

It will ask for password, and then **vi** editor will open and we need to fulfill it in yaml format like this:
```yaml
ansible_become_pass: ""
```

Then, Ansible will create a vault file in the folder: `vault.yaml`


To use the vault variables inside the playbooks, we need to:

- Include the vault into the playbook confi:
```yaml
vars_files:
 - vault.yaml
```

- Add to the `ansible-playbook` execution `--vault-password-file=vault-password.txt`:

```bash
ansible-playbook ... --vault-password-file=vault-password.txt
```

> [!NOTE]
> If we want to edit the vault file:
> ```bash
> ansible-vault edit vault.yaml --vault-password-file=vault-password.txt
> ```

## Launching base ansible playbook

#### Execute the full role
Tags: `b4syk`
```bash
ansible-playbook playbooks/<playbook_name>.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags b4syk --check
```

#### Ensure base packages installed
Tags: `b4syk_packages`
```bash
ansible-playbook playbooks/l<playbook_name>yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags b4syk_packages --check
```

#### Configure useful topics on your favourite shell
1. Configure your favorite shell on the playbook the var `b4syk_shell: <your_favourite_shell>` (by default it is `b4syk_shell: '.zshrc'`)
2. Launch the playbook:

Tags: `b4syk_shell`
```bash
ansible-playbook playbooks/<playbook_name>.yaml -i inventories/localhost.ini --vault-password-file=vault_password.txt --diff --tags b4syk_shell --check
```

#### More
To check more available tasks check [roles/base/tasks/main.yaml](roles/base/tasks/main.yaml)
