# Detection Limitations

Detection engineering requires honesty.

This lab detects patterns and evidence. It does not claim perfect attack coverage.

## Strong Coverage

These are well supported in the current build:

| Area | Evidence |
|---|---|
| SSH success/failure | Ubuntu auth logs and Wazuh built-in rules |
| sudo activity | Ubuntu auth logs and Wazuh built-in rules |
| Docker lifecycle | Wazuh Docker listener |
| FIM changes | Wazuh syscheck |
| Caddy web requests | JSON access logs |
| SQLi/traversal-like web requests | custom Wazuh web rules |
| XSS/command-injection-like requests | custom Wazuh web rules |
| recon/admin discovery | custom Wazuh web rules |
| web brute-force candidates | custom Wazuh frequency rule |
| Nmap/scan behavior | Suricata plus Wazuh correlation |
| Metasploitable syslog | rsyslog forwarding plus Wazuh localfile |

## Partial Or Context-Dependent Coverage

| Area | Limitation |
|---|---|
| Juice Shop challenge-specific attacks | many require app-specific rule tuning |
| DVWA successful exploitation | request pattern is detected, but exploit success needs extra proof |
| file upload attacks | suspicious upload paths can be flagged, but file execution requires more evidence |
| Suricata exploit signatures | depends on enabled rules and visible network traffic |
| encrypted traffic | Suricata sees less application detail if traffic is encrypted |

## Not Covered Yet

The current guide does not fully cover:

- Windows endpoint telemetry
- Sysmon rules
- Active Directory attacks
- Kerberos events
- phishing/email detections
- cloud logs
- malware sandboxing

Those are future phases.

## How To Describe Results

Use careful wording:

| Say This | Avoid This |
|---|---|
| SQLi-like request detected | SQL injection was successful |
| scan behavior detected | attacker compromised the host |
| suspicious upload request detected | web shell executed |
| Metasploitable SSH failure logged | brute force succeeded |

## Next Step

Continue to [What I Learned](./04-what-i-learned.md).
