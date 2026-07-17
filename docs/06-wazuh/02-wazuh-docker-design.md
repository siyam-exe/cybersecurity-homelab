# Wazuh Docker Design

The homelab uses the official Wazuh Docker single-node deployment. This keeps the Wazuh manager, indexer, and dashboard grouped together while leaving the homelab host available for the Wazuh agent, Docker monitoring, rsyslog, Caddy logs, and Suricata later.

## Why Single Node

The homelab has limited memory and CPU, so the single-node stack is the practical fit.

| Option | Fit For This Lab |
|---|---|
| Single-node Docker | Best fit. One manager, one indexer, one dashboard. |
| Multi-node Wazuh | Too heavy for this homelab. |
| Bare-metal Wazuh install | Possible, but less clean to start/stop as one lab stack. |

## Chosen Layout

```text
/home/<HOMELAB_USER>/wazuh-docker/single-node
```

The stack contains:

- `wazuh.manager`
- `wazuh.indexer`
- `wazuh.dashboard`

The homelab host runs the Wazuh agent outside Docker because the agent needs direct access to host logs, FIM paths, package inventory, Docker events, and local files.

## Restart Policy

The Wazuh containers should not auto-start after every reboot.

Use:

```yaml
restart: "no"
```

Reason:

- Wazuh is intentionally on-demand in this homelab.
- The homelab should not come back from reboot already running the heavy SIEM stack.
- Vulnerable apps and Wazuh should be started only when testing or documenting.

## Port Binding Design

The default Wazuh examples expose more than this lab needs. This setup tightens the bindings.

| Wazuh Component | Host Binding | Reason |
|---|---|---|
| Dashboard | `<HOMELAB_TAILSCALE_IP>:443 -> 5601/tcp` | attack_station browser access through Tailscale |
| Manager agent traffic | `<LOCALHOST_BIND_IP>:1514 -> 1514/tcp` | homelab host agent only in this phase |
| Manager enrollment | `<LOCALHOST_BIND_IP>:1515 -> 1515/tcp` | Local enrollment only in this phase |
| Wazuh API | `<LOCALHOST_BIND_IP>:55000 -> 55000/tcp` | Local troubleshooting only |
| Indexer API | `<LOCALHOST_BIND_IP>:9200 -> 9200/tcp` | Local troubleshooting only |
| Wazuh syslog input | not published | Metasploitable syslog uses homelab rsyslog instead |

Use `<LOCALHOST_BIND_IP>` for the loopback bind address on your system. The public docs avoid writing numeric loopback IPs directly.

## Future Agent Note

When Windows or AD agents are added later, the manager agent/enrollment ports may need to be exposed on the Tailscale address and allowed through UFW.

Do not expose those ports broadly.

## Next Step

Continue to [Install Wazuh Stack](./03-install-wazuh-stack.md).
