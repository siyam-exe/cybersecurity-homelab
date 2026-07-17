# Caddy Juice/DVWA Reverse Proxy Example

This is the sanitized final Caddy reverse-proxy design used by the homelab.

Caddy is the logged web entry point for:

- OWASP Juice Shop
- DVWA

This web lab path uses HTTP on private Tailscale-only ports. The Wazuh dashboard itself uses HTTPS, but Juice Shop and DVWA through Caddy are documented as HTTP because `auto_https off` is set in the Caddyfile.

## Host Paths

| Purpose | Path |
|---|---|
| Caddyfile | `/home/<HOMELAB_USER>/caddy/juice-shop/config/Caddyfile` |
| Host log directory | `/var/log/homelab/caddy-juice` |
| Juice Shop access log | `/var/log/homelab/caddy-juice/juice-shop-access.log` |
| DVWA access log | `/var/log/homelab/caddy-juice/dvwa-access.log` |

## Caddyfile

```caddyfile
{
        auto_https off
}

:3002 {
        log {
                output file /var/log/caddy/juice-shop-access.log
                format json
        }

        reverse_proxy juice-shop:3000
}

:8080 {
        log {
                output file /var/log/caddy/dvwa-access.log
                format json
        }

        reverse_proxy dvwa:80
}
```

## Prepare Host Log Directory

```bash
sudo install -d -o root -g adm -m 2775 /var/log/homelab/caddy-juice
sudo touch /var/log/homelab/caddy-juice/juice-shop-access.log
sudo touch /var/log/homelab/caddy-juice/dvwa-access.log
sudo chown root:adm /var/log/homelab/caddy-juice/juice-shop-access.log
sudo chown root:adm /var/log/homelab/caddy-juice/dvwa-access.log
sudo chmod 0640 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo chmod 0640 /var/log/homelab/caddy-juice/dvwa-access.log
```

## Create Container

```bash
docker run -d \
  --name caddy-juice \
  --restart no \
  --network labnet \
  -p <HOMELAB_TAILSCALE_IP>:3002:3002 \
  -p <HOMELAB_TAILSCALE_IP>:8080:8080 \
  -v /home/<HOMELAB_USER>/caddy/juice-shop/config/Caddyfile:/etc/caddy/Caddyfile:ro \
  -v /var/log/homelab/caddy-juice:/var/log/caddy \
  caddy:latest
```

## Design Notes

| Setting | Reason |
|---|---|
| `--name caddy-juice` | Predictable container name |
| `--restart no` | Web lab front door starts only during lab sessions |
| `--network labnet` | Allows reverse proxy by container name |
| `<HOMELAB_TAILSCALE_IP>:3002:3002` | Juice Shop is reachable through Tailscale |
| `<HOMELAB_TAILSCALE_IP>:8080:8080` | DVWA is reachable through Tailscale |
| Caddyfile mounted `:ro` | Container can read config but not edit it |
| log directory bind mount | Wazuh agent can read Caddy JSON access logs from the host |

## Verify

```bash
docker ps -a --filter name=caddy-juice
docker logs caddy-juice
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo tail -n 5 /var/log/homelab/caddy-juice/dvwa-access.log
```
