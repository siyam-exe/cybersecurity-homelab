# Install Wazuh Stack

This section installs the Wazuh manager, indexer, and dashboard with the official single-node Docker deployment.

## Install Required Tools

Run on the homelab:

```bash
sudo apt update
sudo apt install -y git curl
```

Docker and Docker Compose should already be installed from the Docker platform section.

Current Wazuh Docker documentation shows Docker Compose v2 syntax, such as `docker compose`. This lab used the classic `docker-compose` command from the Ubuntu package set because that is what was installed and verified on the homelab.

If your system uses Compose v2, use the same commands but replace `docker-compose` with `docker compose`.

## Set The Required Kernel Parameter

Wazuh indexer needs a higher `vm.max_map_count` value.

Run:

```bash
sudo sysctl -w vm.max_map_count=262144
```

Command breakdown:

| Part | Meaning |
|---|---|
| `sysctl` | Reads or changes Linux kernel settings. |
| `-w` | Writes the setting immediately for the current boot. |
| `vm.max_map_count=262144` | Raises the memory-map limit required by the Wazuh indexer/OpenSearch. |

Make it persistent:

```bash
sudo nano /etc/sysctl.d/99-wazuh.conf
```

Add:

```conf
vm.max_map_count=262144
```

Apply:

```bash
sudo sysctl --system
```

This reloads the files under `/etc/sysctl.d/` so the persistent value is active without waiting for a reboot.

## Clone The Wazuh Docker Repository

Run:

```bash
cd /home/<HOMELAB_USER>
git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.6
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
```

This guide documents the version that was installed and tested in the lab: `v4.14.6`.

Command breakdown:

| Part | Meaning |
|---|---|
| `git clone` | Downloads the Wazuh Docker project. |
| `-b v4.14.6` | Checks out the tested Wazuh release instead of whatever branch is latest later. |
| `single-node` | Uses the all-in-one Wazuh layout suitable for this homelab. |

## Generate Certificates

Run:

```bash
docker-compose -f generate-indexer-certs.yml run --rm generator
```

This creates the certificates needed by the manager, indexer, and dashboard.

Command breakdown:

| Part | Meaning |
|---|---|
| `-f generate-indexer-certs.yml` | Uses Wazuh's certificate-generation compose file instead of the normal stack file. |
| `run --rm generator` | Starts the temporary certificate generator container and removes it after it finishes. |

## Edit The Compose File

Open:

```bash
nano /home/<HOMELAB_USER>/wazuh-docker/single-node/docker-compose.yml
```

Adjust the port bindings and restart policy to match the design:

```yaml
restart: "no"
```

Use explicit host bindings:

```yaml
ports:
  - "<HOMELAB_TAILSCALE_IP>:443:5601"
```

For manager/API/indexer troubleshooting ports, bind locally:

```yaml
ports:
  - "<LOCALHOST_BIND_IP>:1514:1514"
  - "<LOCALHOST_BIND_IP>:1515:1515"
  - "<LOCALHOST_BIND_IP>:55000:55000"
  - "<LOCALHOST_BIND_IP>:9200:9200"
```

Keep Wazuh UDP syslog input unpublished in this phase.

## Start Wazuh

Run:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
```

Command breakdown:

| Part | Meaning |
|---|---|
| `docker-compose up` | Creates and starts the Wazuh containers from the compose file. |
| `-d` | Runs the stack in the background so the terminal is returned to you. |

If you are using Docker Compose v2, the equivalent command is:

```bash
docker compose up -d
```

## Stop Wazuh

For normal stopping:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

For a cleaner compose stop without deleting volumes:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose down
```

Do not use `docker-compose down -v` unless you intentionally want to delete Wazuh volumes and data.

## Health Check

Run:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker ps --filter name='single-node_wazuh' --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

Command breakdown:

| Command | Why it is useful |
|---|---|
| `docker ps --filter name='single-node_wazuh'` | Shows only the Wazuh containers instead of every container on the host. |
| `--format 'table ...'` | Keeps the output readable for screenshots and troubleshooting. |
| `curl -k -I` | Checks whether the dashboard responds while allowing the expected self-signed certificate. |
| `docker exec ... agent_control -l` | Asks the Wazuh manager which agents are registered and active. |

Expected result:

- manager, indexer, and dashboard containers are running
- dashboard returns an HTTP response
- the homelab agent appears after agent enrollment is completed

## Next Step

Continue to [Wazuh Port Bindings](./04-wazuh-port-bindings.md).
