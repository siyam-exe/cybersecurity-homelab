# Playbook: Start Wazuh

Start the Wazuh manager, indexer, and dashboard.

## Commands

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
```

For Docker Compose v2:

```bash
docker compose up -d
```

## Verify

```bash
docker ps --filter name='single-node_wazuh' --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
```

Expected:

- Wazuh containers are running
- `homelab` agent is active
- dashboard responds from `attack_station`
