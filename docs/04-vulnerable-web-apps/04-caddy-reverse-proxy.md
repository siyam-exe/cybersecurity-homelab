# Caddy Reverse Proxy

Caddy is the logged front door for the vulnerable web apps.

It exposes:

| Caddy Port | Target |
|---:|---|
| `3002` | Juice Shop |
| `8080` | DVWA |

Both paths are intended to be reachable through Tailscale only.

## Create The Caddy Config Directory

Run:

```bash
mkdir -p /home/<HOMELAB_USER>/caddy/juice-shop/config
```

## Create The Caddyfile

Open the file:

```bash
nano /home/<HOMELAB_USER>/caddy/juice-shop/config/Caddyfile
```

Add:

```caddyfile
{
        auto_https off
}

:3002 {
        log {
                output file /var/log/caddy/juice-shop-access.log
                format json
        }

        reverse_proxy juice-shop:3000
}

:8080 {
        log {
                output file /var/log/caddy/dvwa-access.log
                format json
        }

        reverse_proxy dvwa:80
}
```

## Format The Caddyfile

Run:

```bash
docker run --rm \
  -v /home/<HOMELAB_USER>/caddy/juice-shop/config/Caddyfile:/etc/caddy/Caddyfile \
  caddy:latest caddy fmt --overwrite /etc/caddy/Caddyfile
```

This checks and formats the Caddyfile using the Caddy container image.

Command breakdown:

| Part | Meaning |
|---|---|
| `docker run --rm` | Starts a temporary Caddy container and removes it when finished. |
| `-v ...:/etc/caddy/Caddyfile` | Mounts the host Caddyfile into the container so Caddy can read and format it. |
| `caddy:latest caddy fmt --overwrite` | Uses the Caddy image to format the file in place. |

## Prepare The Host Log Directory

Run:

```bash
sudo install -d -o root -g adm -m 2775 /var/log/homelab/caddy-juice
sudo touch /var/log/homelab/caddy-juice/juice-shop-access.log
sudo touch /var/log/homelab/caddy-juice/dvwa-access.log
sudo chown root:adm /var/log/homelab/caddy-juice/juice-shop-access.log
sudo chown root:adm /var/log/homelab/caddy-juice/dvwa-access.log
sudo chmod 0640 /var/log/homelab/caddy-juice/juice-shop-access.log
sudo chmod 0640 /var/log/homelab/caddy-juice/dvwa-access.log
```

The `adm` group is used because Ubuntu commonly uses it for readable system logs.

Command breakdown:

| Command | Why it is used |
|---|---|
| `install -d -o root -g adm -m 2775 ...` | Creates the directory with group-readable logging permissions in one step. |
| `touch ...` | Creates each access log file before the container writes to it. |
| `chown root:adm ...` | Keeps the files owned by root while allowing log readers in the `adm` group. |
| `chmod 0640 ...` | Lets root write, lets the group read, and blocks other users. |

## Create The Caddy Container

Run:

```bash
docker run -d \
  --name caddy-juice \
  --restart no \
  --network labnet \
  -p <HOMELAB_TAILSCALE_IP>:3002:3002 \
  -p <HOMELAB_TAILSCALE_IP>:8080:8080 \
  -v /home/<HOMELAB_USER>/caddy/juice-shop/config/Caddyfile:/etc/caddy/Caddyfile:ro \
  -v /var/log/homelab/caddy-juice:/var/log/caddy \
  caddy:latest
```

Command breakdown:

| Part | Meaning |
|---|---|
| `--name caddy-juice` | Gives the reverse proxy a predictable container name. |
| `--restart no` | Keeps the vulnerable lab front door stopped after reboot unless started intentionally. |
| `--network labnet` | Lets Caddy reach `juice-shop` and `dvwa` by container name. |
| `-p <HOMELAB_TAILSCALE_IP>:3002:3002` | Publishes Juice Shop only on the homelab Tailscale address. |
| `-p <HOMELAB_TAILSCALE_IP>:8080:8080` | Publishes DVWA only on the homelab Tailscale address. |
| `-v ...Caddyfile:/etc/caddy/Caddyfile:ro` | Mounts the Caddy config read-only inside the container. |
| `-v /var/log/homelab/caddy-juice:/var/log/caddy` | Stores Caddy access logs on the homelab so Wazuh can read them. |

## Verify Caddy

Run:

```bash
docker ps -a --filter name=caddy-juice
docker logs caddy-juice
```

From the attack_station browser or terminal, test:

```bash
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
curl -I http://<HOMELAB_TAILSCALE_IP>:8080/
```

## Next Step

Continue to [Caddy JSON Access Logs](./05-caddy-json-access-logs.md).
