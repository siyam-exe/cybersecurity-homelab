# After Reboot Checklist

After a reboot, the homelab should come back in a quiet state.

The expected default is:

- SSH running
- Tailscale running
- Wazuh stopped
- Suricata stopped
- vulnerable containers stopped
- Metasploitable powered off

## Check Core Access

Run:

```bash
hostname
uptime
tailscale status
systemctl is-active ssh
systemctl is-active tailscaled
```

Expected:

- `homelab` has the expected hostname
- Tailscale is connected
- SSH is active

## Check Firewall

Run:

```bash
sudo ufw status verbose
```

Confirm the firewall still matches the intended model:

| Access | Expected |
|---|---|
| SSH | allowed on `tailscale0` |
| Wazuh dashboard | allowed on `tailscale0` |
| web lab ports | allowed on `tailscale0`, but containers stopped |
| Metasploitable syslog | allowed only on `vboxnet0` |
| default inbound | denied |

## Check Docker

Run:

```bash
systemctl is-active docker
docker ps
docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
```

Expected:

- Docker service is active
- no vulnerable targets auto-started
- Wazuh containers are stopped unless manually started

## Check Wazuh Agent

Run:

```bash
systemctl is-active wazuh-agent
sudo tail -n 40 /var/ossec/logs/ossec.log
```

The Wazuh agent can run even when the Wazuh Docker stack is stopped. If the manager is stopped, the agent may queue or retry until Wazuh is started.

## Check Suricata

Run:

```bash
systemctl is-enabled suricata
systemctl is-active suricata || true
```

Expected:

```text
disabled
inactive
```

## Check Metasploitable

Run:

```bash
VBoxManage list runningvms
```

Expected:

- Metasploitable is not running unless intentionally started

## Next Step

Continue to [Common Maintenance](./07-common-maintenance.md).
