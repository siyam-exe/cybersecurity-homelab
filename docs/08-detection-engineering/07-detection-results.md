# Detection Results

This page summarizes the final implemented detection state.

It does not include setup detours or temporary issues. Those belong in troubleshooting pages.

## Final Result Summary

| Area | Status | Notes |
|---|---|---|
| Wazuh host agent | Working | homelab appears as an active Ubuntu endpoint. |
| Ubuntu auth/syslog | Working | SSH, PAM, sudo, package/system events visible. |
| Docker monitoring | Working | container lifecycle and Docker exec events visible. |
| Caddy JSON web logs | Working | Juice Shop and DVWA requests visible. |
| Custom web rules | Working | SQLi, traversal, XSS, command injection, recon, upload, brute-force frequency. |
| Metasploitable syslog | Working | forwarded legacy-server logs available. |
| Suricata IDS | Working | EVE alerts ingested by Wazuh. |
| Scan correlation | Working | Nmap/scan detections and repeated scan correlation visible. |
| FIM | Working | watched config path changes visible. |
| Vulnerability Detection | Working | posture visibility available. |

## Web Detection Results

Final working Wazuh web detections:

| Rule ID | Detection | Status |
|---:|---|---|
| `110100` | General Caddy access log | Working |
| `110101` | SQLi-like request | Working |
| `110102` | Path traversal-like request | Working |
| `110103` | XSS-like request | Working |
| `110104` | Command injection-like request | Working |
| `110105` | Recon/admin path discovery | Working |
| `110106` | Suspicious upload/dangerous file request | Working |
| `110107` | Login/brute-force candidate | Working |
| `110108` | Possible web brute force correlation | Working |

Dashboard:

```text
Web Attack Lab
```

## Network IDS And Scan Results

Final working scan detections:

| Detection | Status |
|---|---|
| Suricata alert ingestion into Wazuh | Working |
| Suricata SID `1000002` for Metasploitable scan behavior | Working |
| Suricata SID `1000007` for Tailscale-origin scan behavior | Working |
| Wazuh rule `110120` possible Nmap/port scan | Working |
| Wazuh rule `110121` repeated scan correlation | Working |
| Wazuh rule `110122` scan-like Suricata signature | Available |

Final Nmap validation from `attack_station` produced Wazuh scan detections and populated the expected dashboard fields:

```text
data.src_ip
data.dest_ip
data.dest_port
data.alert.signature_id
data.alert.signature
rule.id
rule.description
```

Dashboard:

```text
Network IDS & Legacy Server
```

## Metasploitable Results

Final working evidence:

| Evidence | Status |
|---|---|
| Metasploitable reachable through approved lab route | Working |
| Metasploitable scan visibility | Working |
| Suricata `vboxnet0` evidence | Working |
| Metasploitable forwarded syslog | Working |
| SSH/insecure connection style Wazuh event during scan activity | Working |

Important interpretation:

```text
Suricata alert = suspicious network traffic matched a rule
Metasploitable syslog = target-side evidence
Both together = stronger investigation story
```

## Host And Docker Results

Final working host/infrastructure detections:

| Area | Example Rules/Fields | Status |
|---|---|---|
| SSH/auth | SSH and PAM built-in groups | Working |
| sudo | built-in sudo rules | Working |
| Docker start/stop | `87903`, `87904` | Working |
| Docker exec | `87907` | Working |
| Docker lifecycle fields | `data.docker.Actor.Attributes.name` | Working |
| FIM | `550`, `553`, `554`, `syscheck.path` | Working |
| Vulnerability Detection | Wazuh state indices | Working |

Dashboard:

```text
Host & Infrastructure Security
```

## Dashboard Result

Final dashboards:

| Dashboard | Purpose |
|---|---|
| Homelab SOC Overview | Central security overview with MITRE, severity, source, and notable alerts. |
| Web Attack Lab | Juice Shop/DVWA web attack detections and raw web evidence. |
| Network IDS & Legacy Server | Suricata, Nmap/scan, destination ports, Metasploitable evidence. |
| Host & Infrastructure Security | SSH, sudo, Docker, FIM, package/system activity. |

The dashboards use readable detection names where possible, especially `rule.description`, so charts do not depend only on raw rule IDs.

## Known Honest Limits

| Limit | Explanation |
|---|---|
| Suricata does not prove exploit success alone | It proves traffic matched a signature. Use target logs too. |
| Web rules are lab detections, not a full WAF | They are intentionally readable and focused for learning. |
| Vulnerability Detection is posture, not attack proof | It shows vulnerable packages, not exploitation. |
| Click/filter behavior may need browser-side review | The self-signed Wazuh certificate can block automated browser checks. |
| More traffic may require tuning | Dashboards should be tuned after more realistic test sessions. |

## Final Idle State

After a test session, the expected idle state is:

```text
vulnerable web containers: stopped
Suricata: inactive
Metasploitable: powered off
Wazuh: stopped unless actively reviewing or testing
```

## Next Step

Continue to [Dashboards](../09-dashboards/01-dashboard-design.md).
