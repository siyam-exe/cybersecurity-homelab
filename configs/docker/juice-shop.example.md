# Juice Shop Container Example

This is the sanitized final Juice Shop container design used by the homelab.

Juice Shop has a local-only diagnostic port, but the normal testing path is through Caddy on port `3002`.

## Pull Image

```bash
docker pull bkimminich/juice-shop
```

## Create Container

```bash
docker run -d \
  --name juice-shop \
  --restart no \
  --network labnet \
  -p <LOCALHOST_BIND_IP>:3001:3000 \
  bkimminich/juice-shop
```

## Design Notes

| Setting | Reason |
|---|---|
| `--name juice-shop` | Predictable container name for scripts, docs, and Docker monitoring |
| `--restart no` | Vulnerable targets should not auto-start after reboot |
| `--network labnet` | Lets Caddy reach Juice Shop by container name |
| `<LOCALHOST_BIND_IP>:3001:3000` | Keeps direct access local to `homelab` for diagnostics |

## Main Access Path

Use this for testing from `attack_station`:

```text
http://<HOMELAB_TAILSCALE_IP>:3002/
```

That path goes through Caddy and produces Wazuh-readable JSON access logs.

## Verify

```bash
docker ps -a --filter name=juice-shop
docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' juice-shop
curl -I http://localhost:3001/
```

Expected:

- container exists
- restart policy is `no`
- local diagnostic path responds after the app starts
