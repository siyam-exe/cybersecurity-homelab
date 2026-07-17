# Wazuh Troubleshooting

This page keeps common Wazuh problems separate from the clean install path.

## Dashboard Does Not Open From attack_station

Check in layers:

```bash
tailscale status
sudo ufw status verbose
docker ps --filter name='single-node_wazuh'
sudo ss -tulpn
```

Confirm:

- attack_station is connected to Tailscale
- homelab is online in Tailscale
- Wazuh dashboard container is running
- port `443/tcp` is allowed on `tailscale0`
- dashboard is bound to `<HOMELAB_TAILSCALE_IP>`

## Browser Shows Certificate Warning

This is expected if the dashboard uses a self-signed certificate.

Accept the warning only when:

- the URL is `https://<HOMELAB_TAILSCALE_IP>/`
- you are on your own Tailnet
- you expect the self-signed Wazuh certificate

## Agent Does Not Appear Active

Check the homelab agent:

```bash
sudo systemctl status wazuh-agent
sudo tail -n 120 /var/ossec/logs/ossec.log
```

Check the manager:

```bash
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

If the manager ports are local-only, the homelab agent should use:

```text
localhost
```

as the manager address.

## Wazuh Containers Do Not Start

Check:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose ps
docker-compose logs --tail=80
```

Also confirm:

```bash
sysctl vm.max_map_count
```

Expected:

```text
vm.max_map_count = 262144
```

## Docker Compose Recreate Error

Classic Docker Compose can sometimes fail when recreating containers.

If you see a container recreation error and the old containers are stopped, remove only the stopped Wazuh containers:

```bash
docker rm <STOPPED_WAZUH_CONTAINER_NAME>
docker-compose up -d
```

Do not remove Wazuh volumes unless you intentionally want to delete data.

## Caddy Logs Do Not Appear In Wazuh

Check the source logs:

```bash
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -n 5 /var/log/homelab/caddy-juice/dvwa-access.log
```

Check Wazuh logcollector:

```bash
sudo tail -n 120 /var/ossec/logs/ossec.log
```

Restart the agent after editing `ossec.conf`:

```bash
sudo systemctl restart wazuh-agent
```

## Docker Listener Does Not Start

Check:

```bash
sudo tail -n 160 /var/ossec/logs/ossec.log | grep -Ei 'docker-listener|ERROR|WARNING'
```

If Python dependency issues appear, reinstall the pinned working package set:

```bash
sudo /var/ossec/framework/python/bin/pip3 install 'docker==6.1.3' 'urllib3==1.26.20' 'requests==2.31.0'
sudo systemctl restart wazuh-agent
```

This pinned set is for the classic `docker-compose` build documented in this lab. If you are rebuilding with Docker Compose v2 and a newer Python environment, compare against the current Wazuh Docker listener package recommendation before pinning older packages.

## FIM Is Too Noisy

Do not add broad paths like the full home directory or Docker storage.

Use small, meaningful paths and add `ignore`/`nodiff` entries where needed.

## Next Step

Continue to [Suricata](../07-suricata/01-why-suricata.md).
