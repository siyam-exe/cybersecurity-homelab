# Backups And Exports

Backups protect work that took time to configure.

This lab does not need to preserve every old test log forever, but it should preserve important configuration and dashboard work.

## What To Back Up

| Item | Why |
|---|---|
| Wazuh Docker compose file | Contains the final port binding and container design |
| Wazuh indexer/dashboard config | Needed for credential and dashboard connectivity recovery |
| Wazuh custom rules | Detection logic for web and scan alerts |
| Wazuh agent config | Log ingestion, FIM, Docker listener settings |
| Suricata config and local rules | IDS interface and local signature logic |
| Caddy config | Reverse proxy and JSON access log design |
| rsyslog config | Metasploitable syslog receiver |
| logrotate files | Keeps log maintenance reproducible |
| Wazuh dashboards export | Preserves dashboard work |

## Create A Backup Folder

Run:

```bash
STAMP=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/home/<HOMELAB_USER>/homelab-change-backups/manual-$STAMP"
mkdir -p "$BACKUP_DIR"
```

Command breakdown:

| Part | Meaning |
|---|---|
| `STAMP=$(date +%Y%m%d-%H%M%S)` | Creates a unique timestamp. |
| `BACKUP_DIR=...` | Stores the backup path in a variable. |
| `mkdir -p` | Creates the folder if needed. |

## Copy Important Files

Run:

```bash
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/docker-compose.yml "$BACKUP_DIR/docker-compose.yml"
sudo cp /var/ossec/etc/ossec.conf "$BACKUP_DIR/ossec.conf"
sudo cp /var/ossec/etc/rules/local_rules.xml "$BACKUP_DIR/local_rules.xml"
sudo cp /etc/suricata/suricata.yaml "$BACKUP_DIR/suricata.yaml"
sudo cp /var/lib/suricata/rules/local.rules "$BACKUP_DIR/suricata-local.rules"
sudo cp /etc/rsyslog.d/20-metasploitable.conf "$BACKUP_DIR/20-metasploitable.conf"
sudo cp /etc/logrotate.d/homelab-caddy-juice "$BACKUP_DIR/homelab-caddy-juice.logrotate"
sudo cp /etc/logrotate.d/homelab-metasploitable "$BACKUP_DIR/homelab-metasploitable.logrotate"
```

If a file does not exist in your build, skip it rather than inventing it.

## Export Wazuh Dashboards

From Wazuh Dashboard:

```text
Main menu
-> Dashboards Management
-> Saved objects
-> Export
```

Export:

- dashboards
- visualizations
- saved searches

Save the export privately first. Only commit it to GitHub after sanitizing it.

## Sanitize Before Publishing

Before putting anything in the public repo, search for:

- real IP addresses
- real usernames
- passwords
- tokens
- private URLs
- browser session details

Public exports belong in:

```text
exports/wazuh-dashboards/
```

## Next Step

Continue to [After Reboot Checklist](./06-after-reboot-checklist.md).
