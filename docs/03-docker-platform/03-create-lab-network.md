# Create Lab Network

The vulnerable web containers and Caddy should share a Docker network. This lets Caddy reach the apps by container name without publishing every app directly to the host.

## Create The Network

Run:

```bash
docker network create labnet
```

## Verify The Network

Run:

```bash
docker network ls
docker network inspect labnet
```

Expected result:

- `labnet` appears in the network list
- the inspect command prints network details
- containers can later join this network

## Why This Matters

The web app flow becomes:

```text
attack_station
-> Tailscale
-> Caddy published port on homelab
-> Docker network
-> Juice Shop or DVWA by container name
```

That is cleaner than exposing every app directly on the host.

## If The Network Already Exists

If Docker says the network already exists, inspect it:

```bash
docker network inspect labnet
```

If it is already the intended lab network, keep it.

## Next Step

Continue to [Container Restart Policy](./04-container-restart-policy.md).
