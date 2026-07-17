# Caddy JSON Access Logs

Caddy access logs are the main web evidence source for Wazuh. They show what was requested, which app was targeted, the HTTP status, and enough request detail to build custom web attack rules later.

## Log Paths

| App Path | Host Log File |
|---|---|
| Juice Shop through Caddy | `/var/log/homelab/caddy-juice/juice-shop-access.log` |
| DVWA through Caddy | `/var/log/homelab/caddy-juice/dvwa-access.log` |

Inside the Caddy container, those files appear under:

```text
/var/log/caddy/
```

The host bind mount keeps them available for Wazuh agent collection later.

## Generate A Test Log

Start the web targets if needed:

```bash
docker start juice-shop dvwa caddy-juice
```

From the attack_station:

```bash
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
```

On the homelab, verify logs:

```bash
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -n 5 /var/log/homelab/caddy-juice/dvwa-access.log
```

Expected result:

- each file receives JSON log entries
- timestamps are present
- requested paths are visible

## Why JSON Logs

JSON logs are easier for Wazuh to decode and for custom rules to match.

Later sections use these logs to detect:

- SQL injection-like strings
- XSS-like strings
- command injection-like strings
- traversal attempts
- upload probes
- recon/admin discovery
- web brute-force frequency

## Log Rotation

Log rotation is configured later in the operations section so these files do not grow forever.

## Next Step

Continue to [Start Stop Web Targets](./06-start-stop-web-targets.md).
