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

## Firewall

`firewall.yml` applies the nftables input firewall from `roles/firewall` (table `inet atalt_filter`, other tables are not touched). Rules come from `firewall_base_rules` (group_vars/all), `firewall_group_rules` (group_vars/<group>.yml) and `firewall_host_rules` (host_vars/<host>.yml). Each apply arms a rollback timer (`firewall_rollback_seconds`) that restores the previous ruleset unless the host is still reachable afterwards.

Canary first, then everything:

    ansible-playbook firewall.yml --limit 10.140.60.252
    ansible-playbook firewall.yml
