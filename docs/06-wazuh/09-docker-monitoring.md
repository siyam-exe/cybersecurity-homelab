# Docker Monitoring

Wazuh Docker monitoring lets the homelab agent detect Docker Engine activity. This is useful because the vulnerable targets are containers and should only run during intentional lab sessions.

## What Docker Monitoring Detects

| Event Type | Why It Matters |
|---|---|
| Container started | Shows when vulnerable targets or Wazuh containers start |
| Container stopped | Confirms lab targets were shut down |
| Container died/killed | Shows unexpected or forced stops |
| Docker network connect | Shows containers joining networks |
| Docker network disconnect | Shows containers leaving networks |
| Docker exec | Shows commands launched inside containers |

## Enable Docker Listener

Open:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add or verify:

```xml
<wodle name="docker-listener">
  <disabled>no</disabled>
  <run_on_start>yes</run_on_start>
  <attempts>5</attempts>
</wodle>
```

## Python Package Compatibility

This lab uses classic `docker-compose`. A newer Docker Python package set caused problems with that Compose version during testing, so the working package set was pinned.

Current Wazuh documentation shows a newer Python Docker package set for modern agent environments. If you are rebuilding the lab with Docker Compose v2 and a newer Python environment, test the current Wazuh package recommendation first. If you are following this exact homelab build, use the pinned set below because it is the one that was verified with the classic Compose setup.

Run:

```bash
sudo /var/ossec/framework/python/bin/pip3 install 'docker==6.1.3' 'urllib3==1.26.20' 'requests==2.31.0'
```

## Restart The Agent

Run:

```bash
sudo systemctl restart wazuh-agent
sudo tail -n 120 /var/ossec/logs/ossec.log
```

Look for Docker listener startup messages.

## Test Docker Events

Start and stop a vulnerable target:

```bash
docker start juice-shop
docker stop juice-shop
```

Expected Wazuh events include:

| Rule ID | Meaning |
|---:|---|
| `87903` | Docker container started |
| `87904` | Docker container stopped |
| `87924` | Container die/kill action |
| `87928` | Docker network connected |
| `87929` | Docker network disconnected |
| `87907` | Docker command launched inside a container |

## Useful Dashboard Filters

```text
rule.id:(87903 OR 87904 OR 87924 OR 87928 OR 87929 OR 87907)
```

Container-specific examples:

```text
data.docker.Actor.Attributes.name:juice-shop
data.docker.Actor.Attributes.name:dvwa
data.docker.Actor.Attributes.name:caddy-juice
```

## Important Limitation

Docker monitoring shows container activity. It does not understand web attacks inside DVWA or Juice Shop by itself.

For web attacks, use:

- Caddy JSON logs
- Wazuh web rules
- Suricata network alerts

## Next Step

Continue to [Credential Rotation](./10-credential-rotation.md).
