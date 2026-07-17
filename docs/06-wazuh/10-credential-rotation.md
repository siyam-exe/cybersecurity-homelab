# Credential Rotation

Wazuh credentials must not be committed to GitHub. This page explains how to handle them safely and how to rotate them later.

## Rules For This Guide

- Do not write real passwords in documentation.
- Do not paste password output into screenshots.
- Do not commit `.env` files or config files that contain secrets unless they are sanitized.
- Back up config files before changing credentials.
- Change one credential set at a time if doing it manually.

## Dashboard Login

The default dashboard username is:

```text
admin
```

The password should be stored in a password manager or another secure place outside the repository.

## View The Current Password On The Homelab

If you need to recover the configured value, inspect it locally on the homelab:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
grep -m1 'INDEXER_PASSWORD=' docker-compose.yml
```

Do not copy the output into GitHub or screenshots.

## Files Commonly Involved

| File | Purpose |
|---|---|
| `/home/<HOMELAB_USER>/wazuh-docker/single-node/docker-compose.yml` | Container environment values |
| `/home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_indexer/internal_users.yml` | Indexer users and password hashes |
| `/home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_dashboard/wazuh.yml` | Dashboard connection settings |

## Users To Understand

| User | Purpose |
|---|---|
| `admin` | Dashboard login and indexer admin user |
| `kibanaserver` | Dashboard-to-indexer service user |
| `wazuh-wui` | Dashboard-to-Wazuh-API service user |

## Rotation Workflow

1. Log out of Wazuh Dashboard.
2. Back up the affected files.
3. Generate a strong password.
4. Update the relevant Docker/config files.
5. Apply indexer security changes if indexer users were changed.
6. Restart Wazuh manager and dashboard.
7. Test login from the attack_station.

## Backup Before Editing

Run:

```bash
STAMP=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/home/<HOMELAB_USER>/homelab-change-backups/wazuh-credentials-$STAMP"
mkdir -p "$BACKUP_DIR"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/docker-compose.yml "$BACKUP_DIR/docker-compose.yml.before"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_indexer/internal_users.yml "$BACKUP_DIR/internal_users.yml.before"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_dashboard/wazuh.yml "$BACKUP_DIR/wazuh.yml.before"
```

Command breakdown:

| Part | Meaning |
|---|---|
| `STAMP=$(date +%Y%m%d-%H%M%S)` | Creates a timestamp so each backup folder has a unique name. |
| `BACKUP_DIR=...` | Stores the backup location in a variable so the later commands are shorter. |
| `mkdir -p "$BACKUP_DIR"` | Creates the backup directory, including parent folders if needed. |
| `cp ... "$BACKUP_DIR/..."` | Copies each important credential-related file before editing. |

## Apply Indexer Security Changes

If you edit indexer users, apply the security config from inside the indexer container:

```bash
docker exec single-node_wazuh.indexer_1 bash -lc 'export INSTALLATION_DIR=/usr/share/wazuh-indexer; export CONFIG_DIR=$INSTALLATION_DIR/config; CACERT=$CONFIG_DIR/certs/root-ca.pem; KEY=$CONFIG_DIR/certs/admin-key.pem; CERT=$CONFIG_DIR/certs/admin.pem; export JAVA_HOME=/usr/share/wazuh-indexer/jdk; HOST=$(grep node.name $CONFIG_DIR/opensearch.yml | awk "{print \$2}"); bash /usr/share/wazuh-indexer/plugins/opensearch-security/tools/securityadmin.sh -cd $CONFIG_DIR/opensearch-security/ -nhnv -cacert $CACERT -cert $CERT -key $KEY -p 9200 -icl -h $HOST'
```

Command breakdown:

| Part | Meaning |
|---|---|
| `docker exec single-node_wazuh.indexer_1` | Runs the command inside the Wazuh indexer container. |
| `bash -lc '...'` | Starts a shell so variables and command substitution work correctly. |
| `INSTALLATION_DIR` and `CONFIG_DIR` | Point the tool to the Wazuh indexer installation and config directories. |
| `CACERT`, `CERT`, and `KEY` | Select the admin TLS certificates used to authenticate the security update. |
| `HOST=$(grep node.name ... | awk ...)` | Reads the indexer node name from the config so the tool targets the correct node. |
| `securityadmin.sh` | Applies the updated OpenSearch security configuration. |
| `-cd $CONFIG_DIR/opensearch-security/` | Tells the tool where the security config files live. |
| `-nhnv` | Skips hostname verification, which is normal for this local Docker certificate setup. |
| `-p 9200 -icl -h $HOST` | Connects to the local indexer API and applies the change to the selected host. |

Restart:

```bash
docker restart single-node_wazuh.manager_1 single-node_wazuh.dashboard_1
```

## Password Character Note

Use strong passwords, but avoid characters that create YAML escaping problems unless you are comfortable escaping them.

A simple safe symbol set for this lab:

```text
.*+?-
```

## Next Step

Continue to [Wazuh Troubleshooting](./11-wazuh-troubleshooting.md).
