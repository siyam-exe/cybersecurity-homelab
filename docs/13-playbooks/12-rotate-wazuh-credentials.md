# Playbook: Rotate Wazuh Credentials

Rotate Wazuh credentials safely.

## Safety Notes

- Back up configuration first.
- Do not paste passwords into documentation.
- Change one credential set at a time.
- Verify dashboard, indexer, API, and agent state afterward.

## Backup First

```bash
STAMP=$(date +%Y%m%d-%H%M%S)
BACKUP_DIR="/home/<HOMELAB_USER>/homelab-change-backups/wazuh-credentials-$STAMP"
mkdir -p "$BACKUP_DIR"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/docker-compose.yml "$BACKUP_DIR/docker-compose.yml.before"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_indexer/internal_users.yml "$BACKUP_DIR/internal_users.yml.before"
cp /home/<HOMELAB_USER>/wazuh-docker/single-node/config/wazuh_dashboard/wazuh.yml "$BACKUP_DIR/wazuh.yml.before"
```

## Rotate Carefully

Follow the full credential guide:

```text
docs/06-wazuh/10-credential-rotation.md
```

## Apply Indexer Security Changes

If `internal_users.yml` was changed, apply the security config from inside the indexer container:

```bash
docker exec single-node_wazuh.indexer_1 bash -lc 'export INSTALLATION_DIR=/usr/share/wazuh-indexer; export CONFIG_DIR=$INSTALLATION_DIR/config; CACERT=$CONFIG_DIR/certs/root-ca.pem; KEY=$CONFIG_DIR/certs/admin-key.pem; CERT=$CONFIG_DIR/certs/admin.pem; export JAVA_HOME=/usr/share/wazuh-indexer/jdk; HOST=$(grep node.name $CONFIG_DIR/opensearch.yml | awk "{print \$2}"); bash /usr/share/wazuh-indexer/plugins/opensearch-security/tools/securityadmin.sh -cd $CONFIG_DIR/opensearch-security/ -nhnv -cacert $CACERT -cert $CERT -key $KEY -p 9200 -icl -h $HOST'
```

## Restart And Verify

```bash
docker restart single-node_wazuh.manager_1 single-node_wazuh.dashboard_1
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```
