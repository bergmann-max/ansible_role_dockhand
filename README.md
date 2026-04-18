# ansible_role_dockhand

[![Ansible](https://img.shields.io/badge/ansible-%3E%3D%202.10-EE0000?logo=ansible&logoColor=white)](https://www.ansible.com/)
[![Platform](https://img.shields.io/badge/platform-Ubuntu-E95420?logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Dockhand](https://img.shields.io/badge/app-dockhand-blueviolet?logo=docker&logoColor=white)](https://github.com/fnsys/dockhand)
[![License](https://img.shields.io/badge/license-Unlicense-blue)](LICENSE)

Ansible role that deploys [Dockhand](https://github.com/fnsys/dockhand), a Docker management UI, via Docker Compose. Integrates with Traefik as reverse proxy. Docker and Traefik must already be present on the target host.

## Requirements

- Ansible >= 2.10
- Docker installed on target host
- Traefik running and connected to a shared Docker network

## Role Variables

### `defaults/main.yml`

| Variable | Default | Description |
|---|---|---|
| `dockhand_image` | `fnsys/dockhand:latest` | Docker image and tag |
| `dockhand_config_dir` | `/opt/dockhand` | Directory for compose file and `.env` |
| `dockhand_data_dir` | `/opt/dockhand/data` | Persistent data volume path |
| `dockhand_user` | `""` | OS user owning config files (required) |
| `dockhand_group` | `docker` | OS group for config files |
| `dockhand_container_name` | `dockhand` | Docker container name |
| `dockhand_port` | `3000` | Internal container port |
| `dockhand_secret_key` | `""` | Session signing secret — **required** |
| `dockhand_database_url` | `""` | PostgreSQL DSN — leave empty for SQLite |
| `dockhand_disable_local_login` | `false` | Disable local password login (e.g. when using OIDC) |
| `dockhand_skip_df_collection` | `false` | Skip disk usage collection |
| `dockhand_domain` | `""` | Public domain for Traefik router rule (required) |
| `dockhand_traefik_entrypoint` | `websecure` | Traefik entrypoint |
| `dockhand_traefik_certresolver` | `cloudflare` | Traefik TLS cert resolver |
| `dockhand_traefik_network` | `""` | External Traefik Docker network name (required) |
| `dockhand_docker_socket` | `/var/run/docker.sock` | Docker socket path mounted into container |
| `dockhand_restart_policy` | `unless-stopped` | Container restart policy |
| `dockhand_extra_env` | `{}` | Extra environment variables passed to container |
| `dockhand_extra_labels` | `{}` | Extra Docker labels applied to container |

## Installation

Add to `requirements.yml`:

```yaml
roles:
  - name: dockhand
    src: git+ssh://git@github.com/bergmann-max/ansible_role_dockhand.git
    version: main
    scm: git
```

Install:

```bash
ansible-galaxy install -r requirements.yml
```

## Example Playbook

```yaml
- hosts: dockhand_host
  roles:
    - role: dockhand
      vars:
        dockhand_user: "deploy"
        dockhand_domain: "dockhand.example.com"
        dockhand_traefik_network: "traefik_proxy"
```

## Tags

| Tag | Description |
|---|---|
| `install` | Creates directories, deploys compose + env files, pulls image, starts service |
| `configure` | Re-deploys `.env` file and restarts container if changed |

## Handlers

| Handler | Trigger | Description |
|---|---|---|
| `Restart dockhand` | compose or env file change | Recreates the container via `docker compose up --force-recreate` |

---

## License

Unlicense

---

## Dependencies

- `community.docker` collection

---

## Author Information

Max Bergmann
