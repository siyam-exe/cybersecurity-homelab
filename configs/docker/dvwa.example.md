# DVWA Container Example

This is the sanitized final DVWA container design used by the homelab.

DVWA does not publish a direct host port. The intended testing path is through Caddy on port `8080`.

## Pull Image

```bash
docker pull vulnerables/web-dvwa
```

## Create Container

```bash
docker run -d \
  --name dvwa \
  --restart no \
  --network labnet \
  vulnerables/web-dvwa
```

## Design Notes

| Setting | Reason |
|---|---|
| `--name dvwa` | Predictable container name for Caddy, scripts, and Docker monitoring |
| `--restart no` | Vulnerable target stays stopped after reboot |
| `--network labnet` | Lets Caddy reach DVWA by container name |
| no `-p` option | Prevents direct host-port access and keeps Caddy as the logged path |

## Main Access Path

Use this for testing from `attack_station`:

```text
http://<HOMELAB_TAILSCALE_IP>:8080/
```

That path goes through Caddy and produces Wazuh-readable JSON access logs.

## Verify

```bash
docker ps -a --filter name=dvwa
docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' dvwa
docker port dvwa
```

Expected:

- container exists
- restart policy is `no`
- `docker port dvwa` shows no direct host port
