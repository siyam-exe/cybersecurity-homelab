# Playbook: Start Web Targets

Start DVWA, Juice Shop, and Caddy for a web testing session.

## Commands

```bash
docker start juice-shop dvwa caddy-juice
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

## Verify From attack_station

```bash
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
```

Expected:

- Juice Shop responds on port `3002`
- DVWA responds on port `8080`
- Caddy writes JSON access logs
