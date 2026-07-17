# Log Rotation

Log rotation prevents lab logs from growing forever.

This lab rotates:

- Caddy Juice Shop access logs
- Caddy DVWA access logs
- Metasploitable forwarded syslog

## Why Rotation Matters

Without rotation:

- Wazuh can keep reading very large log files
- disk usage grows quietly
- old test noise becomes harder to separate from current tests

## Caddy Logrotate File

Create:

```bash
sudo nano /etc/logrotate.d/homelab-caddy-juice
```

Add:

```conf
/var/log/homelab/caddy-juice/*.log {
    weekly
    rotate 4
    compress
    missingok
    notifempty
    create 0640 root adm
    copytruncate
}
```

Config breakdown:

| Option | Meaning |
|---|---|
| `weekly` | Rotate once per week. |
| `rotate 4` | Keep four rotated copies. |
| `compress` | Compress older logs to save disk. |
| `missingok` | Do not error if the file does not exist yet. |
| `notifempty` | Do not rotate empty logs. |
| `create 0640 root adm` | Recreate logs with readable permissions for admin/log readers. |
| `copytruncate` | Copy the current file and truncate it without requiring the container to reopen the file. |

## Metasploitable Logrotate File

Create:

```bash
sudo nano /etc/logrotate.d/homelab-metasploitable
```

Add:

```conf
/var/log/metasploitable/*.log {
    weekly
    rotate 4
    compress
    missingok
    notifempty
    create 0640 syslog adm
}
```

Metasploitable logs are written by rsyslog, so the owner is `syslog`.

## Test Rotation Safely

Run:

```bash
sudo logrotate -d /etc/logrotate.d/homelab-caddy-juice
sudo logrotate -d /etc/logrotate.d/homelab-metasploitable
```

Command breakdown:

| Part | Meaning |
|---|---|
| `logrotate` | Runs logrotate manually. |
| `-d` | Debug mode. Shows what would happen without changing files. |

If the debug output looks clean, force a one-time rotation test only when you are ready:

```bash
sudo logrotate -f /etc/logrotate.d/homelab-caddy-juice
sudo logrotate -f /etc/logrotate.d/homelab-metasploitable
```

After forced rotation, confirm new logs still receive events.

## Wazuh Note

Wazuh logcollector follows configured files. After rotation, generate a small test event and confirm Wazuh still receives it.

For syslog:

```bash
logger "homelab logrotate test"
```

For Caddy logs, make a normal request to Juice Shop or DVWA while the web targets are running.

## Next Step

Continue to [Backups And Exports](./05-backups-and-exports.md).
