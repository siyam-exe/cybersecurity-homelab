# Container Restart Policy

The lab intentionally keeps vulnerable targets stopped unless they are needed for testing. This prevents DVWA, Juice Shop, Caddy lab access, and other testing services from quietly coming back after every reboot.

## Recommended Policy

For vulnerable targets:

```text
--restart no
```

Use this for:

- `juice-shop`
- `dvwa`
- `caddy-juice`

Wazuh can also be kept on-demand if your hardware is limited, especially on an 8 GB system.

## Check Restart Policies

Run:

```bash
docker inspect -f '{{.Name}} {{.HostConfig.RestartPolicy.Name}}' juice-shop dvwa caddy-juice
```

Expected result:

```text
/juice-shop no
/dvwa no
/caddy-juice no
```

## Update A Restart Policy

If a container is already created and needs to be changed:

```bash
docker update --restart no juice-shop
docker update --restart no dvwa
docker update --restart no caddy-juice
```

## Why Not Always-On

The lab is meant for intentional practice:

- start the services
- run attacks/tests
- observe Wazuh and Suricata
- stop the services again

This keeps the homelab quieter and makes the detection environment easier to reason about.

## Next Step

Continue to [Docker Troubleshooting](./05-docker-troubleshooting.md).
