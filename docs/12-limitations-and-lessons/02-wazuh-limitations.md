# Wazuh Limitations

Wazuh is useful, but it does not automatically understand every attack.

This lab uses Wazuh as a SIEM-style platform:

- collect logs
- apply rules
- correlate some repeated events
- display dashboards
- support investigation

## What Wazuh Does Well Here

| Area | Strength |
|---|---|
| Host logs | SSH, PAM, sudo, syslog visibility |
| Docker events | container start, stop, exec, network events |
| FIM | important file change alerts |
| Vulnerability Detection | package/CVE posture visibility |
| Caddy logs | structured web request evidence |
| Suricata alerts | IDS events inside the Wazuh workflow |
| Dashboards | investigation-friendly security views |

## What Wazuh Does Not Prove By Itself

Wazuh does not automatically prove:

- a SQL injection succeeded
- XSS executed in a browser
- command injection returned shell access
- a Metasploit exploit succeeded
- a vulnerability was actively exploited

For those claims, you need supporting evidence:

- application response
- target logs
- shell/session proof
- Suricata signature
- Wazuh rule
- raw event details

## Why Custom Rules Were Needed

Default Wazuh rules understand many host and system events, but DVWA and Juice Shop are intentionally vulnerable web apps.

To make those attacks visible in a clean SIEM style, this lab added custom rules for:

- SQLi-like requests
- traversal-like requests
- XSS-like requests
- command injection-like requests
- recon/admin discovery
- suspicious uploads
- web brute-force frequency
- scan correlation

## Dashboard Limitation

A dashboard is only as good as the fields and detections behind it.

This lab avoids panels that only look impressive but do not answer a useful question.

## Next Step

Continue to [Detection Limitations](./03-detection-limitations.md).
