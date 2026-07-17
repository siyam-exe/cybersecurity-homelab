# Install Docker

This lab uses Docker from the Ubuntu package repositories because the setup is simple and worked cleanly for the homelab. The goal here is not to build a production container platform; it is to run a controlled lab stack reliably.

## Install Docker And Docker Compose

Run:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

Command breakdown:

| Part | Meaning |
|---|---|
| `docker.io` | Docker engine package from Ubuntu repositories. |
| `docker-compose` | Compose tool used by some multi-container stacks. |

This installs the classic `docker-compose` command. Newer Docker installations often use Compose v2 through `docker compose`. The rest of this guide keeps `docker-compose` because that is what was verified on the homelab. If your system has Compose v2 instead, use the same subcommands with a space:

```bash
docker compose version
docker compose up -d
docker compose stop
```

## Enable Docker

Run:

```bash
sudo systemctl enable --now docker
```

This enables Docker at boot and starts it immediately.

## Add Your User To The Docker Group

Run:

```bash
sudo usermod -aG docker <HOMELAB_USER>
```

Command breakdown:

| Part | Meaning |
|---|---|
| `usermod` | Modify a Linux user account. |
| `-aG docker` | Append the user to the `docker` group without removing existing groups. |

Log out and log back in after this command so the group change applies.

## Verify Docker

Run:

```bash
docker --version
docker-compose --version
docker ps
```

If you installed Docker Compose v2 instead, verify it with:

```bash
docker compose version
```

Expected result:

- Docker prints its version
- Docker Compose prints its version
- `docker ps` runs without a permission error

## If Docker Permission Still Fails

If `docker ps` still says permission denied, your current shell may not have the new group membership yet.

Run:

```bash
groups
```

If `docker` is missing, log out completely and reconnect over SSH.

## Next Step

Continue to [Create Lab Network](./03-create-lab-network.md).
