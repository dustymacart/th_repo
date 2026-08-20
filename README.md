Tuttle House Automation Lab

This repository contains automation used in the Tuttle House Lab Environment.
All machines in the Tuttle House environment use the prefix:
th-
This identifies the machine as belonging to the Tuttle House lab.

Purpose

This lab is designed for learning Ansible, Docker, Linux, Windows automation, and related technologies.
The goal is to build reusable automation for tasks such as:
Operating system patching
Package installation and updates
Service restarts
Apache configuration and restarts
IIS configuration and restarts
Firewall configuration
Opening and closing ports
Active Directory integration
Server configuration
System health checks
Configuration validation
User and group management
Application deployment
Security configuration
General Linux and Windows administration

Automation should be developed as reusable Ansible playbooks and roles whenever practical.

Changes should be submitted through pull requests. First cut a branch and make changes. Then create a pull request.
Pull requests require approval from: dustymacart

For questions, contact me through Google using:
arthursdustin@gmail.com

Getting Started
1. Prerequisites

Install the following on your workstation:

Git
Docker Desktop
Visual Studio Code (recommended)

Docker Desktop must be running before starting the Ansible environment.

Verify Docker is working:

docker version

You should see both a Docker Client and Server section.

2. Clone the Repository

Clone the repository to your workstation and enter the repository directory:

git clone <repository-url>
cd <repository-name>

All Ansible work should be performed from the Docker-based development environment.

3. Local Environment Files

The repository uses several local files for authentication and SSH configuration.

The expected structure is:

repo/
├── Dockerfile
├── compose.yml
├── .bashrc
├── .gitignore
│
├── .ansible/
│   └── vault_pass
│
├── .ssh/
│   ├── config
│   └── known_hosts
│
├── inventories/
│   └── production/
│       ├── hosts
│       └── group_vars/
│
└── ansible-facts.yml

Sensitive local files must never be committed to Git.

The .gitignore file should include:

# Ansible Vault password
.ansible/vault_pass

# Kerberos credential cache
.ansible/krb5cc_ansible

# Local SSH host trust
.ssh/known_hosts

# Ansible temporary files
*.retry
4. Configure the Ansible Vault Password

Create the local Ansible directory if it does not already exist:

mkdir -p .ansible

Create the Vault password file:

touch .ansible/vault_pass

Place the Ansible Vault password in:

.ansible/vault_pass

The file should contain only the Vault password.

Do not commit this file to Git.

Verify Git is ignoring it:

git check-ignore -v .ansible/vault_pass
5. Configure SSH Known Hosts

Create the SSH directory and known_hosts file if they do not already exist:

mkdir -p .ssh
touch .ssh/known_hosts

The container uses this file to persist trusted SSH host keys between container sessions.

After entering the container, host keys can be added with:

ssh-keyscan -H \
host1 host2 host3 \
>> /workspace/.ssh/known_hosts

Only add host keys after verifying that they belong to the expected servers.

6. Build the Ansible Container

The development environment is defined by the Dockerfile and compose.yml.

Build it with:

docker compose build

To force a complete rebuild without using cached Docker layers:

docker compose build --no-cache

A rebuild is normally only necessary when the Dockerfile or container dependencies change.

7. Start the Ansible Environment

Start a disposable Ansible development container:

docker compose run --rm ansible

You should be placed directly into:

/workspace

with a prompt similar to:

dcuser /workspace $

The repository on your workstation is mounted at:

/workspace

Changes made to repository files from inside the container are therefore retained on the workstation after the container exits.

8. Verify the Environment

Inside the container:

whoami
pwd
ansible --version
python3 --version
git --version

Expected user:

dcuser

Expected working directory:

/workspace
9. Authenticate to Active Directory

The environment uses Kerberos for domain authentication.

Obtain a Kerberos ticket:

kinit ansiblesvc@DIGITALCANYON.ORG

Enter the domain password when prompted.

Verify the ticket:

klist

A valid ticket should show:

Default principal: ansiblesvc@DIGITALCANYON.ORG

Never store the Active Directory service-account password in the repository.

10. Test Ansible Connectivity

Check the inventory:

ansible-inventory \
  -i inventories/production/ \
  --graph

Test connectivity to the RHEL web servers:

ansible rhwebservers \
  -i inventories/production/ \
  -m ping

A successful response should contain:

SUCCESS
11. Run the Facts Playbook

Run the server information/health-check playbook:

ansible-playbook \
  ansible-facts.yml \
  -i inventories/production/

The Vault password file is configured automatically by the container environment, so --ask-vault-pass should not normally be necessary.

12. Working with Ansible Vault

View an encrypted Vault file:

ansible-vault view \
  inventories/production/group_vars/all/vault.yml

Edit it:

ansible-vault edit \
  inventories/production/group_vars/all/vault.yml

Create a new encrypted file:

ansible-vault create \
  inventories/production/group_vars/all/new-vault.yml

Never place plaintext passwords, API keys, service-account credentials, or other secrets directly into Git.

13. Daily Workflow

Once the environment has been configured, the normal workflow is simple.

Make sure Docker Desktop is running, open a terminal in the repository, and run:

docker compose run --rm ansible

Inside the container, authenticate to the domain:

kinit ansiblesvc@DIGITALCANYON.ORG

Verify the ticket:

klist

Test Ansible:

ansible rhwebservers \
  -i inventories/production/ \
  -m ping

Then run the desired playbook:

ansible-playbook \
  <playbook>.yml \
  -i inventories/production/

When finished:

exit

Because the container is started with --rm, the container itself is removed when you exit.

Your repository, Vault password file, SSH known hosts, and other persistent files remain on your workstation.

Quick Start

After the initial setup, these are the primary commands needed each day:

# Start the Ansible environment
docker compose run --rm ansible

# Authenticate to Active Directory
kinit ansiblesvc@DIGITALCANYON.ORG

# Verify Kerberos
klist

# Verify Ansible connectivity
ansible rhwebservers -i inventories/production/ -m ping

# Run a playbook
ansible-playbook ansible-facts.yml -i inventories/production/
Important

This is a lab environment intended for learning and testing automation.

Even in the lab:

Review playbooks before running them.
Test changes against a single system when possible.
Avoid storing plaintext credentials in Git.
Use Ansible Vault for secrets.
Verify SSH host keys before trusting them.
Use pull requests for changes.
Be especially careful with networking, firewall, domain membership, reboot, and patching automation.