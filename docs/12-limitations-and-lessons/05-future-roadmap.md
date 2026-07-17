# Future Roadmap

This page separates future ideas from the current finished build.

The current project is the Wazuh cybersecurity homelab. The items below should be added only after the core lab stays stable.

## Home Server Additions

| Service | Possible Placement | Notes |
|---|---|---|
| Pi-hole | Docker or dedicated host networking | DNS needs careful port planning. It is not a detection source by itself. |
| Jellyfin | Docker | Use Tailscale or LAN-only access. Avoid heavy transcoding on weak hardware. |
| Samba | host service or container | Host service may be simpler for file permissions. Limit access to trusted networks. |

These are home-server services, not the core SIEM detection lab.

## Security Lab Additions

| Addition | Value |
|---|---|
| Windows endpoint | Windows Event Logs and agent visibility |
| Sysmon | process, network, and registry telemetry |
| Windows Server / AD | realistic authentication and directory-service events |
| Kali VM | more flexible testing workstation |
| More Suricata tuning | better IDS signal and less noise |
| More app-aware web rules | deeper Juice Shop and DVWA challenge detections |

## Dashboard Additions Later

Only add dashboards when the data source exists.

Possible future dashboards:

- Windows Endpoint Security
- Active Directory Security
- DNS/Home Network Overview
- Optional Posture dashboard for SCA and vulnerabilities

Do not add empty dashboards just because they sound impressive.

## Upgrade Path

If hardware is upgraded later, the lab could move toward:

- Wazuh running more often
- more VMs
- Windows/AD telemetry
- longer data retention
- more Suricata rules
- additional endpoint agents

## Final Note

The best next improvement is not another tool.

The best next improvement is repeatable detection testing with clear evidence:

```text
test
-> log source
-> Wazuh rule/signature
-> dashboard panel
-> documented result
```
