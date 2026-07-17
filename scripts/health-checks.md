# Health Check Command Bundle

This is a quick command bundle for checking the homelab state.

Use the detailed explanation in:

```text
docs/10-operations/03-health-checks.md
docs/13-playbooks/09-run-health-checks.md
```

## Core Host Checks

```bash
hostname
uptime
tailscale status
sudo ufw status verbose
```

## Docker Checks

```bash
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker network ls
docker network inspect labnet --format '{{json .Containers}}'
```

## Wazuh Checks

Run when Wazuh is started:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose ps
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
```

Check the host agent:

```bash
sudo systemctl is-active wazuh-agent
sudo tail -n 80 /var/ossec/logs/ossec.log
```

## Suricata Checks

```bash
sudo systemctl is-enabled suricata
sudo systemctl is-active suricata || true
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Expected idle state:

```text
disabled
inactive
```

## Metasploitable Checks

```bash
VBoxManage list runningvms
ping -c 2 <METASPLOITABLE_IP>
```

The ping check only works when Metasploitable is running and routing is available.

## Log Checks

```bash
sudo tail -n 5 /var/log/auth.log
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -n 5 /var/log/homelab/caddy-juice/dvwa-access.log
sudo tail -n 5 /var/log/metasploitable/metasploitable.log
sudo tail -n 5 /var/log/suricata/eve.json
```

Some logs may be quiet or missing if their related services have not been started yet.

## Port Checks

```bash
sudo ss -tulpn
```

Expected model:

- SSH on Tailscale
- Wazuh dashboard on Tailscale only when Wazuh is running
- web lab ports only when Caddy/web targets are running
- Wazuh manager/API/indexer local-only
