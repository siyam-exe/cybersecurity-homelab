# Network IDS & Legacy Server

The Network IDS & Legacy Server dashboard focuses on Suricata and Metasploitable.

It answers:

```text
What network-level activity and legacy-server evidence did the SIEM see?
```

![Network IDS and Legacy Server dashboard](../../assets/dashboards/network-ids-legacy-server.png)

## Dashboard Purpose

| Purpose | Explanation |
|---|---|
| Network IDS investigation | Show Suricata alerts, signatures, interfaces, ports, sources, and destinations. |
| Scan visibility | Show Nmap/port-scan style detections and correlation. |
| Legacy-server visibility | Show Metasploitable syslog and SSH/auth evidence. |
| Raw evidence | Keep IDS and legacy logs available for investigation. |

## Panel Layout

| Section | Panels |
|---|---|
| KPI row | Total IDS alerts, Nmap/scan detections, Metasploitable syslog, Metasploitable SSH/auth |
| Charts | IDS alerts over time, interface split, Suricata signatures, top destination ports, source IPs, destination IPs, Metasploitable SSH/syslog events |
| Bottom table | Raw IDS and legacy logs |

## Detection Coverage

| Detection | Source |
|---|---|
| Suricata alert | Wazuh rule `86601` |
| Metasploitable port sweep | Suricata SID `1000002` |
| Tailscale-origin scan | Suricata SID `1000007` |
| Wazuh scan detection | Wazuh `110120` |
| Repeated scan correlation | Wazuh `110121` |
| Scan-like signature text | Wazuh `110122` |
| Metasploitable syslog | forwarded syslog file |
| Metasploitable SSH/auth style evidence | Wazuh SSH/syslog rules |

## Important Fields

| Panel | Fields |
|---|---|
| IDS alert count | `rule.groups`, `rule.id` |
| Suricata signatures | `data.alert.signature`, `data.alert.signature_id` |
| Interface split | `data.in_iface` |
| Source IPs | `data.src_ip` |
| Destination IPs | `data.dest_ip` |
| Destination ports | `data.dest_port` |
| Protocols | `data.proto` |
| Legacy logs | `location`, `rule.description`, `rule.id` |

## Why Interface Split Matters

Suricata monitors:

| Interface | Meaning |
|---|---|
| `tailscale0` | Remote testing traffic entering over Tailscale. |
| `vboxnet0` | Metasploitable host-only traffic. |

This matters because the same test path can produce different source context depending on where the traffic is observed.

For investigation:

```text
Need original Tailscale-side source?
-> check tailscale0 events
```

```text
Need Metasploitable-side network evidence?
-> check vboxnet0 events
```

## How To Use It

For an Nmap test:

1. Start Suricata.
2. Start Metasploitable if testing the legacy VM.
3. Run the scan from `attack_station` or Kali.
4. Open Network IDS & Legacy Server.
5. Check Nmap/scan detections.
6. Review source IPs, destination ports, and Suricata signatures.
7. Open raw IDS and legacy logs.

Example flow:

```text
Nmap scan
-> Suricata SID fires
-> Wazuh 110120 fires
-> repeated scan 110121 may fire
-> dashboard shows destination ports and raw IDS logs
```

## Honest Limits

Suricata alerts do not automatically prove exploitation.

Use this rule:

```text
Suricata = suspicious traffic evidence
Metasploitable syslog = target-side evidence
Both together = stronger investigation
```

## Next Step

Continue to [Host & Infrastructure Security](./05-host-infrastructure-security.md).
