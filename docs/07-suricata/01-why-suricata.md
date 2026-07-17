# Why Suricata

Wazuh already gives the lab strong host and log visibility, but it does not automatically see every network-level pattern by itself. Caddy logs can show HTTP requests. Ubuntu logs can show SSH and sudo activity. Metasploitable syslog can show what the legacy server recorded.

Suricata adds a different layer: network IDS visibility.

In this lab, Suricata is used to detect and describe traffic such as:

| Activity | Why Suricata Helps |
|---|---|
| Nmap scans | Sees source, destination, protocol, destination ports, and IDS signatures. |
| Port sweeps | Helps show scan-like behavior even before an exploit is attempted. |
| Metasploitable probing | Adds network evidence next to Metasploitable syslog. |
| Juice Shop and DVWA traffic | Adds network evidence next to Caddy/Wazuh web logs. |
| Suspicious HTTP payloads | Can trigger signatures for traversal, SQLi-like strings, or other web patterns. |

## What Suricata Is Not Replacing

Suricata does not replace the Wazuh agent, Caddy logs, Docker monitoring, FIM, or Metasploitable syslog.

It complements them.

| Evidence Type | Best Source |
|---|---|
| Exact requested URL and HTTP status | Caddy JSON logs |
| Source/destination IPs and ports | Suricata EVE alerts |
| Docker container start/stop/exec | Wazuh Docker listener |
| Host login, sudo, SSH | Ubuntu logs through Wazuh |
| File changes | Wazuh FIM |
| Legacy VM service logs | Metasploitable syslog |

For a realistic SIEM workflow, the value comes from comparing sources. A scan alert from Suricata tells you suspicious network traffic happened. A syslog event or Caddy event tells you what the target saw. Together, they make the evidence stronger.

## Placement In This Lab

Suricata runs directly on `homelab`, not inside Docker.

That design was chosen because Suricata needs direct visibility into host network interfaces:

```text
attack_station
-> Tailscale
-> homelab tailscale0
-> Caddy / Docker targets
```

```text
attack_station or Kali
-> Tailscale subnet route
-> homelab vboxnet0
-> Metasploitable
```

The two important monitored interfaces are:

| Interface | Purpose |
|---|---|
| `vboxnet0` | Metasploitable host-only network traffic. |
| `tailscale0` | Remote testing traffic from `attack_station` over Tailscale. |

## On-Demand Runtime Model

Suricata is not kept running all the time in this homelab.

The final model is:

```text
Suricata installed
-> disabled at boot
-> started only during network IDS testing
-> stopped after testing
```

Why:

- packet capture is only needed during lab sessions
- the homelab hardware is limited
- vulnerable targets are also on-demand
- the lab stays quieter when not actively testing

## Final Log Flow

The Suricata-to-Wazuh path is:

```text
Suricata
-> /var/log/suricata/eve.json
-> Wazuh agent localfile
-> Wazuh manager
-> Wazuh indexer
-> Network IDS & Legacy Server dashboard
```

Wazuh sees Suricata alerts under the Suricata/IDS rule groups and can be searched with:

```text
rule.groups:suricata
```

## Official References

This lab follows the same general model as the official Suricata quickstart and Wazuh Suricata integration guide, adjusted for the homelab's interfaces and on-demand runtime model:

- [Suricata Quickstart](https://docs.suricata.io/en/latest/quickstart.html)
- [Wazuh Network IDS Integration](https://documentation.wazuh.com/current/proof-of-concept-guide/integrate-network-ids-suricata.html)

## Next Step

Continue to [Install Suricata](./02-install-suricata.md).
