# cron

Standalone Ansible role for Debian. Licensed under MIT. Authors: Aymen Gazzah.

## Installation

```yaml
roles:
  - name: gazzah.cron
    src: https://github.com/gazzah-lab/ansible-role-cron.git
    version: v1.0.1
```

Run `ansible-galaxy role install -r requirements.yml`. Requires ansible-core >= 2.15, collected facts, and root privilege. Runtime smoke tests use Debian 13; other releases declared in metadata require validation in your environment.

## Inventory configuration

Store variables in `group_vars/all/gazzah.cron.yml`, override in group or host directories. These filenames are conventions: Ansible loads the variable contents.

```yaml
cron_manage_service: false
cron_jobs:
- name: Example cleanup
  cron_file: example
  user: root
  minute: '15'
  hour: '3'
  job: /usr/bin/true
```

The `manage_service` / `manage_systemd` false values above are for container tests; use their default true on real machines.

Jobs sharing `cron_file` are rendered together. Set `disabled` per job or `cron_jobs_disabled` globally. Only files bearing the role marker are removed as orphans. An empty job list is refused unless `cron_allow_empty: true`. Validation also runs with `--tags cron-cleanup`. Use `cron_foreign_files` to reserve filenames managed by another tool.

## Variables

| Variable | Default |
| --- | --- |
| `cron_packages` | `['cron']` |
| `cron_jobs` | `[]` |
| `cron_jobs_disabled` | `False` |
| `cron_config_dir` | `/etc/cron.d` |
| `cron_default_user` | `root` |
| `cron_file_mode` | `0644` |
| `cron_managed_marker` | `ANSIBLE MANAGED - gazzah.cron` |
| `cron_remove_orphans` | `True` |
| `cron_foreign_files` | `[]` |
| `cron_service` | `cron` |
| `cron_apt_install_state` | `present` |
| `cron_apt_update_cache` | `True` |
| `cron_apt_cache_valid_time` | `3600` |
| `cron_allow_empty` | `False` |
| `cron_manage_service` | `True` |

See [defaults/main.yml](defaults/main.yml) for comments and [tests/test.yml](tests/test.yml) for a runnable playbook.

## Testing

```sh
python -m pip install 'ansible-core>=2.20,<2.21' ansible-lint yamllint
yamllint -c .yamllint .
ansible-lint --offline -c .ansible-lint .
```

CI also runs the example twice in a disposable Debian 13 container and checks the second run has no changes. No automatic package upgrade, reboot, or cron job execution is triggered by these tests.
