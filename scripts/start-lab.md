# Start Lab Command Bundle

This is a copy-paste friendly command bundle for starting a full lab session.

Use the detailed explanation in:

```text
docs/10-operations/01-start-lab.md
docs/13-playbooks/01-start-wazuh.md
docs/13-playbooks/03-start-web-targets.md
docs/13-playbooks/05-start-suricata.md
docs/13-playbooks/07-start-metasploitable.md
```

## Start Wazuh

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
docker ps --filter name='single-node_wazuh' --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

## Start Web Targets

```bash
docker start juice-shop dvwa caddy-juice
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

## Start Suricata

Only start Suricata when IDS visibility is needed:

```bash
sudo systemctl start suricata
sudo systemctl is-active suricata
```

## Start Metasploitable

Only start Metasploitable when legacy-server testing is needed:

```bash
VBoxManage startvm metasploitable2 --type headless
VBoxManage list runningvms
```

## Quick Access Checks

From `attack_station` or an equivalent testing shell:

```bash
curl -I https://<HOMELAB_TAILSCALE_IP>/
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
```

Expected:

- Wazuh dashboard responds on HTTPS
- Juice Shop responds through Caddy on port `3002`
- DVWA responds through Caddy on port `8080`

## Notes

- Keep vulnerable targets stopped unless testing.
- If you only need host/Wazuh work, skip web targets, Suricata, and Metasploitable.
- If using Docker Compose v2, replace `docker-compose` with `docker compose`.
