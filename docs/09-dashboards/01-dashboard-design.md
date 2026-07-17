# Dashboard Design

The final Wazuh dashboard setup uses four dashboards.

The goal is to feel like a small SOC environment without creating too many dashboards that repeat the same evidence.

## Final Dashboard Set

| Dashboard | Purpose |
|---|---|
| Homelab SOC Overview | Central security overview and first screen after login. |
| Web Attack Lab | Juice Shop and DVWA web attack investigation. |
| Network IDS & Legacy Server | Suricata, Nmap/scan activity, and Metasploitable evidence. |
| Host & Infrastructure Security | SSH, sudo, Docker, FIM, package/system, and host activity. |

These are the final names. Do not keep duplicate experimental dashboard copies in the finished guide.

## Why Four Dashboards

Four dashboards gives a clean split:

| Need | Dashboard |
|---|---|
| Quick security summary | Homelab SOC Overview |
| Web attack investigation | Web Attack Lab |
| Network and legacy-server investigation | Network IDS & Legacy Server |
| Infrastructure and host monitoring | Host & Infrastructure Security |

This avoids creating separate small dashboards for Docker, FIM, Suricata, Metasploitable, and web apps when those views are more useful grouped together.

## Final Object Names

| Dashboard | Saved Object ID |
|---|---|
| Homelab SOC Overview | `homelab-soc-overview` |
| Web Attack Lab | `web-attack-lab` |
| Network IDS & Legacy Server | `network-ids-legacy-server` |
| Host & Infrastructure Security | `host-infrastructure-security` |

## Final Panel Counts

| Dashboard | Panel Count |
|---|---:|
| Homelab SOC Overview | 15 |
| Web Attack Lab | 16 |
| Network IDS & Legacy Server | 12 |
| Host & Infrastructure Security | 13 |

Final saved object set:

```text
4 dashboards
52 visualizations
4 raw investigation saved searches
```

## Design Rules

The dashboards follow these rules:

- use detection rules and Suricata signatures instead of loose keyword filters where possible
- use source IP aggregations from event fields, not a hardcoded attacker address
- show readable detection names with `rule.description` where possible
- keep MITRE tactics and techniques in the SOC overview
- keep raw investigation panels only where they support drill-down
- remove old duplicate dashboards after final verification
- keep Pi-hole out of the detection dashboards for this phase

## Important Field Choices

Some fields are easy to get wrong.

| Data Type | Correct Field |
|---|---|
| Caddy source label | `data.homelab.source` |
| Web app label | `data.homelab.app` |
| Web request URI | `data.request.uri` |
| Web client IP | `data.request.client_ip` |
| Suricata signature | `data.alert.signature` |
| Suricata SID | `data.alert.signature_id` |
| Suricata interface | `data.in_iface` |
| Suricata source IP | `data.src_ip` |
| Suricata destination IP | `data.dest_ip` |
| Suricata destination port | `data.dest_port` |
| Docker container name | `data.docker.Actor.Attributes.name` |
| FIM path | `syscheck.path` |

One dashboard issue was fixed by using `data.docker.Actor.Attributes.name` instead of `docker.Actor.Attributes.name`.

Another chart was improved by using `rule.description` instead of `rule.id`, so the web attack chart shows names instead of raw numbers.

## Where To Open Dashboards

From Wazuh Dashboard:

```text
Main menu
-> Dashboards
-> Dashboards
-> search for Homelab, Web Attack, Network IDS, or Host
```

Direct placeholder URLs:

```text
https://<HOMELAB_TAILSCALE_IP>/app/dashboards#/view/homelab-soc-overview
https://<HOMELAB_TAILSCALE_IP>/app/dashboards#/view/web-attack-lab
https://<HOMELAB_TAILSCALE_IP>/app/dashboards#/view/network-ids-legacy-server
https://<HOMELAB_TAILSCALE_IP>/app/dashboards#/view/host-infrastructure-security
```

## Next Step

Continue to [Homelab SOC Overview](./02-homelab-soc-overview.md).
