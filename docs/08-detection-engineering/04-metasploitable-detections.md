# Metasploitable Detections

Metasploitable represents the legacy vulnerable server in the lab.

It gives the SIEM something different from Juice Shop and DVWA:

- exposed legacy services
- SSH/auth evidence
- service enumeration
- scan traffic
- forwarded syslog
- Suricata network alerts

## Evidence Sources

| Source | What It Shows |
|---|---|
| Suricata on `vboxnet0` | Traffic hitting Metasploitable on the host-only network. |
| Suricata on `tailscale0` | Original Tailscale-side testing source when traffic enters the homelab. |
| Metasploitable syslog | Target-side legacy Linux logs forwarded to homelab. |
| Wazuh built-in SSH/syslog rules | Auth events, insecure connection attempts, service-side events. |

## Expected Detection Types

| Activity | Expected Evidence |
|---|---|
| Ping/connectivity test | Suricata ICMP test alert if local test rule is enabled. |
| Nmap scan | Suricata scan signatures and Wazuh scan correlation. |
| SSH failed login | Metasploitable syslog and Wazuh auth/SSH rules. |
| Service enumeration | Suricata destination ports and possible protocol signatures. |
| Exploit attempt | Suricata signature if traffic matches a loaded rule, plus target logs if the service records it. |

## Important Limitation

Do not claim exploit success from a Suricata alert alone.

A network IDS alert proves suspicious traffic matched a rule. It does not always prove the target was exploited.

For a stronger conclusion, compare:

```text
Suricata alert
-> Metasploitable syslog
-> Wazuh alert
-> dashboard raw evidence
```

## Final Working Scan Evidence

The final scan validation showed:

| Detection | Result |
|---|---|
| Suricata alerts | Worked |
| Wazuh Suricata rule `86601` | Worked |
| Wazuh scan rule `110120` | Worked |
| Wazuh repeated scan rule `110121` | Worked |
| Metasploitable SSH/insecure connection rule `5706` | Worked during scan activity |

Metasploitable service ports were visible in the scan evidence, including common legacy services such as FTP, SSH, Telnet, HTTP, SMB, database services, VNC, IRC, and Tomcat.

## Queries To Use In Wazuh

Search for Suricata IDS evidence:

```text
rule.groups:suricata
```

Search for custom scan detections:

```text
rule.id:(110120 OR 110121 OR 110122)
```

Search for Metasploitable forwarded syslog:

```text
data.homelab.source:metasploitable
```

Search for SSH/auth events:

```text
rule.groups:sshd OR rule.groups:authentication_failed OR rule.groups:authentication_success
```

## Dashboard Placement

Metasploitable appears mainly in:

```text
Network IDS & Legacy Server
```

Useful panels:

| Panel | Why |
|---|---|
| Nmap/scan detections | Shows scan activity against lab targets. |
| Top destination ports | Shows which legacy services were touched. |
| Suricata signatures | Shows IDS alert names. |
| Interface split | Separates `tailscale0` and `vboxnet0`. |
| Metasploitable syslog/auth | Shows target-side evidence. |
| Raw IDS/legacy logs | Lets you inspect the exact event. |

## Next Step

Continue to [Host And Docker Detections](./05-host-and-docker-detections.md).
