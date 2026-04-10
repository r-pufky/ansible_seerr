# [Seerr][o]
Seerr install from public release tarball.

## [Requirements][i]
Requires [r_pufky.arr][g] galaxy-ng collection.

* RAM: ~2GB
* Install size: ~3.4GB

See [FAQ for initial authentication configuration][p].

## Role Variables
Detailed variable use documented in defaults. See usage for role operation.

* [defaults][j] - User configurable options.

* [ports][k] - Ports are **not** managed (defined for external use).

## Usage
Existing databases are migrated automatically when new versions are installed.
[Migration between database platforms][q] is not supported and must be done
manually.

### WARNING
> Role no longer [directly modifies databases][r] to prevent errors from DB
> changes through Seerr version upgrades. The REST API is not available until
> the [initial configuration is complete][p] even when configured in service
> environment.

### NOTE
>[Migrations from Jellyseerr/Overseerr][q] in practice require manual
> intervention.
>
> Always backup databases and data (or make a snapshot) before attempting a
> migration. Suggest creating a new container and removing the old instance one
> confirmed working.

### Database
PostgreSQL 14+ is highly recommended over default SQLite database.

Postgres requires pre-existing databases with root privileges to initialize
tables and manage migrations. Postgres databases are NOT backed up by Seerr and
must be managed outside of this role. An example Seerr postgres [.env][m] is
provided within the role. See [authoritative instructions][l].

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                  | Notes
 ------|-----------------------|-------
  1    | seerr_flg_backup      | Backup config data. Exits role.
  2    | seerr_flg_restore     | Restore config data. Exits role.
  3    | seerr_flg_maintenance | Preform role maintenance tasks.
  4    | seerr_flg_install     | Install required packages, users, etc.
  5    | seerr_flg_config      | Install user-defined config.

### Example Playbooks
Seerr will automatically generate a environment file if one is not provided.
Example configuration files are located in [files][n]. These **must** be named
**.env**.

#### New Deployments
``` yaml
# First time setup requires post-install WebUI configuration. This cannot be
# avoided without direct database edits due to architecture.
#
# Config data: /var/opt/seerr/config.
- name: 'Seerr default install.'
  ansible.builtin.include_role:
    name: 'r_pufky.arr.seerr'
```

#### Static Deployments
Config data is deployed as templates, allowing for vault use of sensitive
information. **However** this will deploy static files and overwrite existing
DB's, etc. Existing installs likely want to use **backup** and **restore**.

``` yaml
# Include your environment in .env.
- name: 'Statically deploy Seerr with pre-existing DB and config.'
  ansible.builtin.include_role:
    name: 'r_pufky.arr.seerr'
  vars:
    seerr_flg_config: true
    seerr_cfg_d: 'host_vars/seerr.example.com/data'
```

#### Dynamic Deployments
Use **backup** and **restore** to carry over existing data to a new instance;
or leave seerr_flg_config disabled to leave existing config untouched.

``` yaml
- name: 'Upgrade Seerr version without touching configuration.'
  ansible.builtin.include_role:
    name: 'r_pufky.arr.seerr'
  vars:
    seerr_srv_version: 'v4.0.17.NEW_VERSION'
    seerr_flg_config: false

- name: 'Create backup.'
  ansible.builtin.include_role:
    name: 'r_pufky.arr.seerr'
  vars:
    seerr_flg_backup: true
    seerr_cfg_backup_d: '/tmp'

- name: 'Restore from backup.'
  ansible.builtin.include_role:
    name: 'r_pufky.arr.seerr'
  vars:
    seerr_flg_restore: true
    seerr_cfg_backup_d: '/tmp'
```

## Development
Configure [environment][a].

``` bash
# Run all tests.
molecule test --all
```

Testing variables:

  Variable            | Type | Description
 ---------------------|------|-------------
  molecule_flg_inject | bool | Disable **get_url** to inject files locally.

### [Releases][b]

  Release | Debian | Ansible | Seerr | Notes
 ---------|--------|---------|-------|-------
  6.x.x   | 13     | 2.20    | 3.1.0 | Ansible 2.20, feature flags, and semantic versioning.
  5.x.x   | 13     | 2.18    | 2.7.3 | Migrate to Seerr.
  4.x.x   | 13     | 2.18    | N/A   | Migrate to Debian Trixie.
  3.x.x   | 12     | 2.18    | N/A   | Migrate to Jellyseerr.
  2.x.x   | 12     | 2.18    | N/A   | Use libraries for common operations.
  1.x.x   | 12     | 2.11    | N/A   | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License][c] | [direct link][f]

## Author Information
PGP: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9][d] | [github gist][e]


[a]: https://r-pufky.github.io/ansible_docs
[b]: https://semver.org/spec/v2.0.0
[c]: https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0
[d]: https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9
[e]: https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22

[f]: https://github.com/r-pufky/ansible_seerr/blob/main/LICENSE
[g]: https://github.com/r-pufky/ansible_collection_arr
[i]: https://github.com/r-pufky/ansible_seerr/blob/main/meta/main.yml
[j]: https://github.com/r-pufky/ansible_seerr/tree/main/defaults/main/main.yml
[k]: https://github.com/r-pufky/ansible_seerr/blob/main/defaults/main/ports.yml
[l]: https://docs.seerr.dev/extending-seerr/database-config#postgresql-options
[m]: https://github.com/r-pufky/ansible_seerr/blob/main/templates/postgres/.env
[n]: https://github.com/r-pufky/ansible_seerr/blob/main/templates/
[o]: https://seerr.dev
[p]: https://docs.seerr.dev/using-seerr/users/owner
[q]: https://docs.seerr.dev/migration-guide
[r]: https://github.com/r-pufky/ansible_seerr/blob/ecc5903d1d970ef2c7f297e69990c764b5f86e8d/tasks/set_owner.yml