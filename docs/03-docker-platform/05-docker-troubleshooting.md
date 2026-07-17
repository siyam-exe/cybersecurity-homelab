# Docker Troubleshooting

This page covers the common Docker problems that can happen while building the lab.

## Permission Denied Running Docker

**Symptom**

`docker ps` returns a permission error.

**Fix**

Confirm your user is in the Docker group:

```bash
groups
```

If `docker` is missing:

```bash
sudo usermod -aG docker <HOMELAB_USER>
```

Then log out and reconnect.

## Container Name Already Exists

**Symptom**

Docker says a container name is already in use.

**Check existing containers:**

```bash
docker ps -a
```

If the old container is not needed, remove it:

```bash
docker rm <CONTAINER_NAME>
```

If the container is running, stop it first:

```bash
docker stop <CONTAINER_NAME>
docker rm <CONTAINER_NAME>
```

## Port Already Allocated

**Symptom**

Docker cannot start a container because a port is already in use.

**Check listening ports:**

```bash
sudo ss -tulpn
```

Then stop or reconfigure the service using the conflicting port.

## Network Already Exists

**Symptom**

`docker network create labnet` says the network already exists.

**Fix**

Inspect the existing network:

```bash
docker network inspect labnet
```

If it is the intended lab network, keep using it.

## Container Starts But App Is Not Ready

Some applications take time to become ready after the container starts.

Check logs:

```bash
docker logs <CONTAINER_NAME>
```

Then test again after a short wait.

## Next Step

Continue to [Vulnerable Web Apps](../04-vulnerable-web-apps/01-web-lab-overview.md).
