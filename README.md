# Jellyseerr
Jellyseerr install from public release tarball.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_jellyseerr/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_jellyseerr/tree/main/defaults/main)

### Ports
All ports and protocols have been defined for the role.

[defaults/ports.yml](https://github.com/r-pufky/ansible_jellyseerr/blob/main/defaults/main/ports.yml)

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.srv](https://github.com/r-pufky/ansible_collection_srv) collection.

## Example Playbook
**STOP.**

WARNING
> Jellyseerr & Overseerr are now officially merging together.
>
> Upstream repositories **will** change: https://github.com/seerr-team/seerr
> Role repository migration to: https://github.com/r-pufky/seerr
> Action Required:
> Role name WILL change to r_pufky.arr.seerr during collection
> migration. Be sure to update the collection and role.

WARNING
> Migrations from Overseerr in practice require manual intervention.
>
> ALWAYS backup databases and data (or make a snapshot) before
> attempting a migration.
>
> Suggest creating a new container and removing the old instance one confirmed
> working.
>
> [migration instructions](docs.jellyseerr.dev).

Read defaults documentation.

Overseerr migrations should be done **manually**: Overseerr versions > `1.33.2`
may not be migratable.

Install 2.7.3 release of Jellyseerr; Use Plex as a media server and ensure
media files have proper permissions. Version (and databases) will be migrated
and updated on new releases. Configure both Sonarr and Radarr services.

``` yaml
- name: 'Jellyseerr server'
  hosts: 'jellyseerr.example.com'
  become: true
  roles:
     - 'r_pufky.srv.jellyseerr'
  vars:
    forgejo_srv_version: 'v2.7.3'
    jellyseerr_srv_owner_email: 'jellyseerr@example.com'
    jellyseerr_srv_owner_username: 'jellyseerr'
    jellyseerr_srv_owner_password: '{PASSWORD}'
    jellyseerr_srv_owner_avatar: 'https://plex.tv/users/1234567890abdcef/avatar?c=1234567890'
    jellyseerr_cfg_plex_name: ''  # See defaults, Plex/Jellyfin supported.
    jellyseerr_cfg_plex_ip: ''
    jellyseerr_cfg_plex_port: 32400
    jellyseerr_cfg_plex_use_ssl: false
    jellyseerr_cfg_plex_machine_id: '{PLEX PROCESSED MACHINE ID}'
    jellyseerr_cfg_client_id: '{{ vault_jellyseerr_client_id }}'
    jellyseerr_cfg_api_key: '{{ vault_jellyseerr_api_key }}'
    jellyseerr_plex_radarr:
      - name: 'radarr'
        hostname: '127.0.0.1'
        port: 7878
        api_key: '{RADARR API KEY}'
        use_ssl: false
        active_profile_id: 1
        active_profile_name: 'defined profile'
        active_directory: '/data/media'
        is_4k: false
        minimum_availability: 'released'
        tags: []
        is_default: true
        external_url: ''
        url_base: ''
        sync_enabled: true
        prevent_search: true
        tag_requests: false
        id: 0
    jellyseerr_plex_sonarr:
      - name: 'sonarr'
        hostname: '127.0.0.1'
        port: 8989
        api_key: '{SONARR API KEY}'
        use_ssl: false
        active_profile_id: 1
        active_profile_name: 'defined profile'
        active_directory: '/data/media'
        active_language_profile_id: 1
        active_anime_profile_id: 1
        active_anime_profile_name: 'defined profile'
        active_anime_directory: '/data/media'
        active_anime_language_profile_id: 1
        tags: []
        anime_tags: []
        is_4k: false
        is_default: true
        enable_season_folders: true
        external_url: ''
        url_base: ''
        sync_enabled: true
        prevent_search: true
        tag_requests: false
        id: 0
```

The Jellyseerr role can deploy config only changes after the first run, greatly
increasing deployment speed (useful for updating settings from configuration
and adding users). Apply config only changes during a run:

```bash
ansible-playbook site.yml --tags Jellyseerr -e '{"jellyseerr_srv_force_config_only_enable": true}'
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_docs/blob/main/ansible/environment.md)

Run all unit tests:
``` bash
molecule test --all
```

### Releases
Release format: **{OS}-{SERVICE}-{ROLE}**

Each type inherits the versioning system used; defaulting to schematic
versioning.

`12.0.0-2.0.3-1.0.0`

* 12.0.0 - Debian 12 (bookworm).
* 2.0.3 - Service/app version.
* 1.0.0 - Role version.

Releases are branched on Debian releases:

* **[13.x.x](https://github.com/r-pufky/ansible_jellyseerr)**: 13 Trixie.
* **[12.x.x](https://github.com/r-pufky/ansible_jellyseerr/tree/12.x)**: 12 Bookworm.

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_jellyseerr/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
