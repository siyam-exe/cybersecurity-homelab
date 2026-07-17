# Health Checks

Health checks confirm whether the lab is ready for testing or safely idle.

Use them before a test session, after a reboot, and after troubleshooting.

## Docker

Run:

```bash
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker network ls
docker network inspect labnet --format '{{json .Containers}}'
```

Command breakdown:

| Part | Meaning |
|---|---|
| `docker ps --format ...` | Shows running containers in a readable table. |
| `docker network ls` | Confirms Docker networking exists. |
| `docker network inspect labnet` | Confirms which containers are attached to the lab network. |

## Wazuh

Run:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose ps
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

Check dashboard reachability:

```bash
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
```

Expected result:

- containers are running when Wazuh is intentionally started
- `homelab` agent is active
- dashboard returns an HTTP response

## Wazuh Agent

Run:

```bash
systemctl is-active wazuh-agent
sudo tail -n 80 /var/ossec/logs/ossec.log
```

Look for logcollector messages showing configured log files are being analyzed.

## Logs

Check active log files:

```bash
sudo tail -n 5 /var/log/auth.log
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -n 5 /var/log/homelab/caddy-juice/dvwa-access.log
sudo tail -n 5 /var/log/metasploitable/metasploitable.log
sudo tail -n 5 /var/log/suricata/eve.json
```

Some files may not exist or may be quiet if the related service has not been started yet.

## Suricata

Run:

```bash
systemctl is-active suricata || true
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Expected result:

- `inactive` when idle
- `active` only during IDS testing
- config test exits cleanly

## Metasploitable

Run:

```bash
VBoxManage list runningvms
ping -c 2 <METASPLOITABLE_IP>
```

The ping test only works when Metasploitable is running and the route is available.

## Ports

Run:

```bash
sudo ss -tulpn
sudo ufw status verbose
```

Expected exposure:

| Service | Expected Exposure |
|---|---|
| SSH | Tailscale only |
| Wazuh Dashboard | Tailscale only when Wazuh is running |
| Juice Shop through Caddy | Tailscale only when web targets are running |
| DVWA through Caddy | Tailscale only when web targets are running |
| Wazuh manager/API/indexer | local-only |
| Metasploitable syslog | `vboxnet0` only |

## Next Step

Continue to [Log Rotation](./04-log-rotation.md).
