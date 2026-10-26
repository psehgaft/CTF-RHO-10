# CTF-RHO-10

Ansible playbooks practicing Red Hat system-administration tasks, in the
style of a capture-the-flag / lab exercise (CTF-RHO-10). Each playbook
covers one administration topic and targets lab hosts — run them only
against disposable lab machines, never against production systems.

## Playbooks

All playbooks live under `ansible/`.

| Playbook | What it does |
| --- | --- |
| `ansible/network.yml` | Configures a Chrony NTP client (server `3.north-america.pool.ntp.org`, via the `templates/chrony_client.conf.j2` template), installs and enables Cockpit, and schedules a cron job (`runningaway`, every 5 minutes) that logs the Cockpit service status to `/var/log/automation.log`. Targets `server2`. |
| `ansible/partition.yaml` | Creates a 1 GiB XFS partition on `/dev/nvme1n1`, mounts it at `/extdrive`, and sets the directory group to `IT`. Targets `server2`. |
| `ansible/users-and-qroups.yml` | Creates groups and users from loops (Sales, IT, Support, Services), with per-group membership taken from `ansible/group_vars/user_groups.yml`. Targets all hosts. |

Supporting files:

- `ansible/group_vars/user_groups.yml` — group membership for Sales,
  Support, Services, and IT.
- `ansible/templates/chrony_client.conf.j2` — Chrony client configuration
  used by `network.yml`.

## Requirements

- Ansible (control node).
- Managed hosts reachable over SSH (the playbooks target `server2` and,
  for users/groups, all hosts).
- The `ansible.posix` and `community.general` collections (used by
  `partition.yaml`).
- Root or sudo access on the managed hosts (`become` is used where needed).

## Usage

This repository ships no inventory file — point Ansible at your own lab
inventory. From the repository root, for example:

```sh
ansible-playbook -i <your-inventory> ansible/network.yml
ansible-playbook -i <your-inventory> ansible/partition.yaml
ansible-playbook -i <your-inventory> ansible/users-and-qroups.yml
```

> **Warning:** `partition.yaml` partitions and formats a disk
> (`/dev/nvme1n1`) and mounts it at `/extdrive`. Double-check the device
> name against your lab machine before running it — running it against the
> wrong disk destroys data.

## License

This project is licensed under the MIT License. See the `LICENSE` file for
details.
