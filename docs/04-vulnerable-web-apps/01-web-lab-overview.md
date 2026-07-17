# Web Lab Overview

The web lab contains two intentionally vulnerable applications:

- DVWA
- OWASP Juice Shop

They are used to generate realistic web attack evidence for Wazuh dashboards and detection rules.

## Final Web Architecture

```text
attack_station browser or Kali tools
-> Tailscale
-> homelab Caddy container
-> Docker labnet
-> Juice Shop or DVWA
-> Caddy JSON access logs
-> Wazuh agent later
```

## Why Caddy Is In Front

Caddy is the main web access path because it gives consistent JSON access logs for both vulnerable apps.

That matters because Wazuh can read one clean log format instead of trying to normalize separate app logs from each target.

## Web App Roles

| Component | Role |
|---|---|
| `juice-shop` | Modern vulnerable web application for web attack practice. |
| `dvwa` | Classic vulnerable PHP app for beginner-friendly attack categories. |
| `caddy-juice` | Reverse proxy and JSON log source for both web apps. |
| `labnet` | Docker network that lets Caddy reach the apps by container name. |

## Exposure Rules

| Path | Intended Use |
|---|---|
| Juice Shop direct local port | homelab-side diagnostic only. |
| Juice Shop through Caddy | Main testing path. |
| DVWA through Caddy | Main testing path. |
| DVWA direct host port | Not used. |

DVWA should not publish a host port directly. That keeps attack traffic on the Caddy logging path.

## What This Enables Later

This setup supports Wazuh detections for:

- SQL injection-like requests
- XSS-like requests
- command injection-like requests
- traversal-like requests
- suspicious upload attempts
- brute-force-like web activity
- recon and admin path discovery
- top attacked URLs
- source IP and target app breakdowns

## Next Step

Continue to [DVWA Deployment](./02-dvwa-deployment.md).
