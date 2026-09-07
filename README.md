# Linux Mint Workstations

Ansible configuration for centrally managed Linux Mint Cinnamon
workstations used in company stores.

## Architecture

Git is the source of truth for workstation configuration.

Ansible defines and applies the desired workstation state.

The golden image is only a deployment optimization and is not the
configuration source of truth.

## Ansible control node

Current control node:

- OS: Debian 13
- Hostname: ansible-server
- Ansible Core: 2.19
- Git repository: `~/git/linux-mint-workstations`

## Managed workstation accounts

- `itadmin` - manual and emergency administrator
- `ansible` - automation account
- `pokladna` - normal store employee account

## Environments

Two inventories are maintained:

- `inventory/test` - development and testing
- `inventory/production` - production store workstations

The default inventory configured in `ansible.cfg` is the test
environment.

Production must be selected explicitly.

## Basic commands

Test connectivity:

```bash
ansible all -m ping
