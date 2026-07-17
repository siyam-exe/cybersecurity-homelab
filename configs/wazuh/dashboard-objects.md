# Dashboard Objects

This file records the final dashboard saved object names used by the homelab.

## Final Dashboards

| Dashboard | Saved Object ID | Purpose |
|---|---|---|
| Homelab SOC Overview | `homelab-soc-overview` | Central SOC overview |
| Web Attack Lab | `web-attack-lab` | Juice Shop and DVWA web attack investigation |
| Network IDS & Legacy Server | `network-ids-legacy-server` | Suricata, Nmap/scan, and Metasploitable evidence |
| Host & Infrastructure Security | `host-infrastructure-security` | SSH, sudo, Docker, FIM, and infrastructure events |

## Final Object Counts

```text
4 dashboards
52 visualizations
4 raw investigation saved searches
```

## Important Fields

| Purpose | Field |
|---|---|
| Web request URI | `data.request.uri` |
| Web client IP | `data.request.client_ip` |
| Web app split | `data.homelab.app` |
| Suricata signature | `data.alert.signature` |
| Suricata SID | `data.alert.signature_id` |
| Suricata interface | `data.in_iface` |
| Docker container name | `data.docker.Actor.Attributes.name` |
| FIM path | `syscheck.path` |

## Notes

- Do not keep old experimental dashboard copies in the final exported set.
- Prefer `rule.description` in chart legends when the alternative is raw rule numbers.
- Keep raw investigation saved searches only where they help explain alerts.
- Sanitize exports before committing them to GitHub.
