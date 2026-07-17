# Juice Shop Deployment

OWASP Juice Shop is deployed on the same Docker network as DVWA and Caddy. It has one local-only diagnostic port, but the main testing path is through Caddy.

## Pull The Image

Run:

```bash
docker pull bkimminich/juice-shop
```

## Create The Container

Run:

```bash
docker run -d \
  --name juice-shop \
  --restart no \
  --network labnet \
  -p <LOCALHOST_BIND_IP>:3001:3000 \
  bkimminich/juice-shop
```

Replace `<LOCALHOST_BIND_IP>` with your system's loopback bind address. This keeps the direct Juice Shop port local to the homelab instead of exposing it as the main lab path.

## Verify The Container

Run:

```bash
docker ps -a --filter name=juice-shop
docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' juice-shop
```

Expected result:

- `juice-shop` exists
- restart policy is `no`
- the direct diagnostic port is local-only

## Test The Diagnostic Path From homelab

Run this on the homelab:

```bash
curl -I http://localhost:3001/
```

Expected result:

- HTTP headers are returned
- the app may take a short time to become ready after startup

## Why This Is Not The Main Attack Path

The diagnostic path is useful for checking whether Juice Shop itself is running. It should not be the main attack/testing path because it bypasses Caddy logging.

Use Caddy on port `3002` for normal lab testing.

## Next Step

Continue to [Caddy Reverse Proxy](./04-caddy-reverse-proxy.md).
