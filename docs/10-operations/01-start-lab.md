# Start Lab

This page explains the normal startup order for a lab session.

The homelab is designed to stay quiet by default. Start only the pieces needed for the test you are doing.

## Startup Order

Use this order for a full detection session:

1. Start Wazuh.
2. Confirm the homelab agent is active.
3. Start the target service you want to test.
4. Start Suricata only if network IDS visibility is needed.
5. Start Metasploitable only if legacy-server testing is needed.
6. Generate controlled test traffic.
7. Review Wazuh dashboards and raw logs.

## Start Wazuh

Run on `homelab`:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
```

If your system uses Docker Compose v2:

```bash
docker compose up -d
```

Verify:

```bash
docker ps --filter name='single-node_wazuh' --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

Expected result:

- Wazuh manager, indexer, and dashboard containers are running
- the `homelab` agent appears active after it checks in
- the dashboard opens from `attack_station` through Tailscale

## Start Web Targets

For Juice Shop and DVWA testing:

```bash
docker start juice-shop dvwa caddy-juice
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

Expected access paths:

| Target | Access Path |
|---|---|
| Juice Shop | `http://<HOMELAB_TAILSCALE_IP>:3002/` |
| DVWA | `http://<HOMELAB_TAILSCALE_IP>:8080/` |

Caddy should be the logged access path for both apps.

## Start Suricata

Start Suricata only when you want network IDS evidence:

```bash
sudo systemctl start suricata
systemctl is-active suricata
```

Expected result:

```text
active
```

Verify that EVE JSON is being written:

```bash
sudo tail -n 5 /var/log/suricata/eve.json
```

## Start Metasploitable

Start Metasploitable only for legacy-server testing:

```bash
VBoxManage startvm metasploitable2 --type headless
VBoxManage list runningvms
```

Expected result:

- the VM appears in the running VM list
- `homelab` can reach `<METASPLOITABLE_IP>` over `vboxnet0`
- `attack_station` can reach `<METASPLOITABLE_IP>` if the Tailscale subnet route is approved

## Quick Full Session Check

After startup, run:

```bash
docker ps
systemctl is-active wazuh-agent
systemctl is-active suricata
VBoxManage list runningvms
```

Not every service needs to be active. The important part is that the services needed for the current test are active and the rest are intentionally stopped.

## Next Step

Continue to [Stop Lab](./02-stop-lab.md).
