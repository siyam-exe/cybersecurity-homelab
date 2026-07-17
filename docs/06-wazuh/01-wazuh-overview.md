# Wazuh Overview

Wazuh is the SIEM/XDR platform for this homelab. It receives logs and security events from the homelab, stores them in the Wazuh indexer, and displays them in the Wazuh dashboard from the attack_station.

## What Wazuh Does In This Lab

| Area | What Wazuh Shows |
|---|---|
| Ubuntu host logs | SSH activity, sudo activity, PAM/authentication events, syslog events |
| Caddy JSON logs | Juice Shop and DVWA web requests, web attack rules, target app labels |
| Metasploitable syslog | Legacy server service logs, SSH/auth events, forwarded syslog evidence |
| Docker monitoring | Container starts, stops, network attach/detach, `docker exec` activity |
| File Integrity Monitoring | Changes to selected homelab configuration paths |
| Vulnerability Detection | Package/CVE inventory for the homelab |
| Suricata alerts | Network scan and IDS evidence from `eve.json` |

## Why Wazuh Is Used

Wazuh gives the lab a SIEM-like path because it already understands agents, rules, vulnerabilities, FIM, authentication logs, Docker events, and dashboards.

The final GitHub guide therefore documents Wazuh as the intended SIEM platform.

## Main Wazuh Components

| Component | Purpose |
|---|---|
| Wazuh manager | Receives events, applies decoders/rules, manages agents |
| Wazuh indexer | Stores alerts, inventories, and dashboard data |
| Wazuh dashboard | Web UI used from the attack_station |
| Wazuh agent | Installed on the homelab host to collect local logs and telemetry |

## On-Demand Model

This homelab does not run Wazuh all the time. The homelab has limited resources, so Wazuh is started when doing lab work and stopped afterward.

That is different from a real company, where a SIEM should normally run continuously. Here the goal is controlled learning, not permanent monitoring.

## Final Access Model

```text
attack_station browser
-> Tailscale
-> <HOMELAB_TAILSCALE_IP>
-> Wazuh Dashboard
```

The dashboard is reachable through Tailscale. The manager, API, and indexer are kept local to the homelab unless a future agent rollout needs more exposure.

## Next Step

Continue to [Wazuh Docker Design](./02-wazuh-docker-design.md).
