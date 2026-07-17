# Juice Shop Tests

Juice Shop tests prove the second web target uses the same logged path as DVWA.

The intended flow is:

```text
attack_station
-> Caddy Juice Shop port
-> Caddy JSON access log
-> Wazuh web rules
-> Web Attack Lab dashboard
```

## Requirements

Start:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
docker start juice-shop caddy-juice
sudo systemctl start suricata
```

## Normal Juice Shop Request

From `attack_station`:

```bash
curl -i http://<HOMELAB_TAILSCALE_IP>:3002/
```

Expected:

- Caddy access log entry
- Wazuh general Caddy rule
- app split shows `juice-shop`

## SQLi-Like Search Request

From `attack_station`:

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:3002/?q=%27%20OR%201%3D1--"
```

Expected:

- SQLi-like Wazuh rule
- Web Attack Lab SQLi panel
- raw web log row with the URI

## Traversal-Like Request

From `attack_station`:

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:3002/%2e%2e/%2e%2e/etc/passwd"
```

Expected:

- traversal-like Wazuh rule
- top attacked URI panel update
- optional Suricata traversal signature if enabled rule matches

## Recon/Admin Discovery

From `attack_station`:

```bash
curl -s -o /dev/null http://<HOMELAB_TAILSCALE_IP>:3002/admin
curl -s -o /dev/null http://<HOMELAB_TAILSCALE_IP>:3002/robots.txt
curl -s -o /dev/null http://<HOMELAB_TAILSCALE_IP>:3002/.env
```

Expected:

- recon/admin discovery rule
- Web Attack Lab recon/admin panel

## Challenge-Specific Note

Juice Shop has many application-specific challenges. This lab does not claim every Juice Shop challenge has a custom Wazuh rule.

The dashboard focuses on common SIEM-style web signals:

- SQLi-like requests
- traversal-like requests
- XSS-like requests
- recon/admin discovery
- suspicious uploads
- brute-force-like frequency
- app split and raw URI evidence

More app-aware Juice Shop rules can be added later after testing specific challenge traffic.

## Next Step

Continue to [Metasploitable Tests](./05-metasploitable-tests.md).
