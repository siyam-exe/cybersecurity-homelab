# Playbook: Stop Wazuh

Stop Wazuh without deleting data.

## Commands

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

For Docker Compose v2:

```bash
docker compose stop
```

## Verify

```bash
docker ps --filter name='single-node_wazuh'
```

Expected:

- no running Wazuh containers
- Wazuh volumes and data are still present

Do not use `down -v` unless you intentionally want to delete stored Wazuh data.
