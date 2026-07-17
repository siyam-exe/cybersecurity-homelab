# Start Stop Web Targets

The vulnerable web targets should run only during lab sessions.

## Start The Web Lab

Run:

```bash
docker start juice-shop dvwa caddy-juice
```

Then check:

```bash
docker ps --filter name=juice-shop
docker ps --filter name=dvwa
docker ps --filter name=caddy-juice
```

## Test Access

From the attack_station:

```bash
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
```

Expected result:

- Juice Shop responds through Caddy on port `3002`
- DVWA responds through Caddy on port `8080`
- Caddy logs are written on the homelab

## Stop The Web Lab

Stop Caddy first, then the apps:

```bash
docker stop caddy-juice juice-shop dvwa
```

## Confirm They Are Stopped

Run:

```bash
docker ps -a --filter name=juice-shop
docker ps -a --filter name=dvwa
docker ps -a --filter name=caddy-juice
```

Expected result:

- all three containers show `Exited`
- ports `3002` and `8080` are not listening unless Caddy is running

## Why Stop Caddy First

Caddy is the exposed web entry point. Stopping it first closes the remote testing path before the backend containers stop.

## Next Step

Continue to [Web App Troubleshooting](./07-web-app-troubleshooting.md).
