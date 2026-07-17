# DVWA Deployment

DVWA is deployed as a Docker container on the internal lab network. It does not publish a direct host port. The only intended browser path is through Caddy.

## Pull The Image

Run:

```bash
docker pull vulnerables/web-dvwa
```

## Create The Container

Run:

```bash
docker run -d \
  --name dvwa \
  --restart no \
  --network labnet \
  vulnerables/web-dvwa
```

Command breakdown:

| Part | Meaning |
|---|---|
| `--name dvwa` | Gives the container a predictable name. |
| `--restart no` | Keeps the target from starting automatically after reboot. |
| `--network labnet` | Lets Caddy reach DVWA by container name. |
| No `-p` option | Prevents direct host-port access. |

## Verify The Container

Run:

```bash
docker ps -a --filter name=dvwa
docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' dvwa
```

Expected result:

- the container exists
- restart policy is `no`
- no host port is published

## Why No Direct Port

Direct DVWA access would bypass Caddy and reduce the quality of Wazuh web attack evidence.

The intended flow is:

```text
attack_station
-> Tailscale
-> Caddy port 8080
-> dvwa container
-> Caddy JSON access log
```

## Next Step

Continue to [Juice Shop Deployment](./03-juice-shop-deployment.md).
