# Docker Port Binding Strategy

Docker port bindings decide where a container service listens on the homelab. This should be designed before depending on firewall rules.

The clean approach is:

```text
Docker binding decides where the service can listen.
UFW decides which interfaces are allowed to reach it.
```

## Final Web Target Binding Plan

| Container | Binding Goal | Reason |
|---|---|---|
| `juice-shop` | local-only diagnostic port | Direct access exists only for homelab-side testing. |
| `caddy-juice` | Tailscale-bound ports for Juice Shop and DVWA | attack_station reaches the apps through logged Caddy paths. |
| `dvwa` | no host port | DVWA should not bypass Caddy logging. |

## Why Caddy Is The Main Web Path

Caddy gives one clean place to log web requests before Wazuh ingests them.

Without Caddy, the lab would need to collect web logs differently from each app. With Caddy, both Juice Shop and DVWA can create consistent JSON access logs.

## Port Plan

| Port | Service | Intended Reachability |
|---:|---|---|
| `22/tcp` | SSH | Tailscale only |
| `443/tcp` | Wazuh dashboard | Tailscale only |
| `3001/tcp` | Juice Shop direct diagnostic path | Local-only |
| `3002/tcp` | Juice Shop through Caddy | Tailscale only |
| `8080/tcp` | DVWA through Caddy | Tailscale only |
| `514/udp` | Metasploitable syslog to homelab | VirtualBox host-only only |

## Why DVWA Has No Direct Host Port

DVWA is intentionally vulnerable. If it publishes a host port directly, users can bypass Caddy and the clean Wazuh web log path.

The better design is:

```text
attack_station
-> Tailscale
-> Caddy on homelab
-> DVWA container on Docker network
-> Caddy JSON log
-> Wazuh
```

## Why Juice Shop Still Has A Local Diagnostic Port

Juice Shop has a local-only diagnostic path so the homelab can test whether the app itself is alive without going through Caddy.

That path should not be the main user path and should not be used for attack testing, because it bypasses Caddy logs.

## Next Step

Continue to [Remote Access Troubleshooting](./05-remote-access-troubleshooting.md).
