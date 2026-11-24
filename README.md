# atalt-network

Ansible playbooks for the atalt hosts. The single role `roles/base` installs base packages and an OpenTelemetry Collector (otelcol-contrib) on every host. The collector sends host metrics and logs (journald, fail2ban) to Splunk HEC.

## Control host prerequisites

Install the required collections:

    ansible-galaxy collection install -r requirements.yml

Create the vault password file `~/.ansible/vault_pass` (never commit it):

    mkdir -p ~/.ansible
    (umask 077; openssl rand -base64 32 > ~/.ansible/vault_pass)

`ansible.cfg` reads this file automatically.

## Vault

The Splunk HEC token is not stored in the repo. Create the encrypted vault file:

    ansible-vault create group_vars/all/vault.yml

It must contain:

    vault_splunk_hec_token: <token>

## Run

    ansible-playbook install_base.yml

## Checks

    ansible-lint
    ansible-playbook --syntax-check install_base.yml
