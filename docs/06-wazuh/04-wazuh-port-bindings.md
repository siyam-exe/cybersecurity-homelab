# Wazuh Port Bindings

Wazuh should be reachable from the attack_station without exposing unnecessary services. The dashboard is the only Wazuh service intentionally reachable through Tailscale in this phase.

## Final Port Map

| Service | Port | Exposure |
|---|---:|---|
| Wazuh Dashboard | `443/tcp` | Tailscale only |
| Wazuh manager agent traffic | `1514/tcp` | Local homelab only |
| Wazuh manager enrollment | `1515/tcp` | Local homelab only |
| Wazuh API | `55000/tcp` | Local homelab only |
| Wazuh indexer API | `9200/tcp` | Local homelab only |
| Wazuh syslog input | `514/udp` | Not published |

## UFW Rule For Dashboard

Run:

```bash
sudo ufw allow in on tailscale0 to any port 443 proto tcp
sudo ufw status verbose
```

This allows the attack_station to open the Wazuh dashboard through Tailscale.

## Dashboard Access From attack_station

Open:

```text
https://<HOMELAB_TAILSCALE_IP>/
```

The first certificate is self-signed unless you later configure a cleaner internal certificate. Accept it only if the address is your homelab and the certificate warning is expected.

## Login Credentials

Do not commit Wazuh passwords to GitHub.

The default dashboard username is:

```text
admin
```

The password should be generated, stored securely, and rotated when needed. Credential handling is documented later in [Credential Rotation](./10-credential-rotation.md).

## What Not To Expose Yet

Do not add public or LAN-wide rules for:

- `1514/tcp`
- `1515/tcp`
- `55000/tcp`
- `9200/tcp`
- Wazuh `514/udp`

Future Windows or AD agents can be added later through Tailscale with explicit rules.

## Next Step

Continue to [Install Host Agent](./05-install-host-agent.md).
