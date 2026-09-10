# PostgreSQL role

This role installs PostgreSQL and its Python driver on every workstation. It
starts and enables PostgreSQL only when `workstation_role` is `server`.
PostgreSQL is stopped and disabled on cash-register workstations.

The role manages:

- service state;
- `listen_addresses`;
- PostgreSQL users;
- databases;
- all rules in `pg_hba.conf`.

The default HBA policy permits only local Unix socket and loopback connections.
Add remote networks in host or store variables; never use `0.0.0.0/0` unless a
separate firewall policy has been reviewed and approved.

## Vault-backed users

`inventory/test/group_vars/all/main.yml` maps `postgresql_users` to the existing
encrypted `vault.yml`. Edit that file only with:

```bash
ansible-vault edit inventory/test/group_vars/all/vault.yml
```

Add the real database accounts using this structure:

```yaml
vault_postgresql_users:
  - name: REAL_DATABASE_USERNAME
    password: "REAL_DATABASE_PASSWORD"
    role_attr_flags: LOGIN,NOSUPERUSER,NOCREATEDB,NOCREATEROLE,NOREPLICATION
    state: present
```

User-management output is hidden with `no_log: true` because each loop item may
contain a password.

## Remote clients

For a server that must accept store-LAN clients, set both values in its host or
store variables after confirming the real subnet:

```yaml
postgresql_listen_addresses: "*"
postgresql_hba_additional_rules:
  - contype: host
    databases: pokladna
    users: REAL_DATABASE_USERNAME
    address: REAL_STORE_SUBNET
    method: scram-sha-256
    comment: Pokladna clients on the store LAN
```

## Validation

```bash
ansible-galaxy collection install -r requirements.yml

ansible-playbook playbooks/workstation.yml \
  --limit SERVER0-TES \
  --tags postgresql \
  --check \
  --diff \
  --ask-vault-pass
```

Run the same command without `--check` for the pilot, then run it a second time.
The second run should report no changes.
