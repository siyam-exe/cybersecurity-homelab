# Project Introduction

This project is a personal cybersecurity homelab built around Wazuh, vulnerable lab targets, and SOC-style detection practice.

The lab uses older hardware on purpose. Instead of building a large enterprise environment, the goal is to create a small but realistic setup where attacks can be generated safely, logs can be collected, and detections can be reviewed in Wazuh dashboards.

## What This Project Is

This homelab is a private learning environment for:

- Linux server administration
- Docker-based vulnerable application hosting
- Wazuh SIEM setup and tuning
- Suricata network IDS visibility
- log collection and detection engineering
- attack simulation against owned lab targets
- dashboard-based investigation

The final lab is designed around this flow:

```text
Controlled test activity
-> vulnerable target or host service
-> logs and alerts
-> Wazuh rules
-> Wazuh dashboards
-> investigation and tuning
```

This matters because running an attack is only half of the learning. The more useful question is:

> What evidence did the attack leave behind, and can I prove what happened from the defender side?

## Why This Homelab Was Built

The main goal was to turn spare hardware into a practical cybersecurity lab that feels closer to a small company environment than a random collection of tools.

In this setup:

- the Dell acts like a small company server
- the Acer acts like the admin and analyst workstation
- DVWA and Juice Shop act like vulnerable internal web applications
- Metasploitable acts like a legacy vulnerable server
- Wazuh acts like the SIEM
- Suricata adds network IDS visibility

The lab is not meant to be always-on production infrastructure. Vulnerable targets, Wazuh, Suricata, and Metasploitable are started only when needed. This keeps the attack surface smaller and also respects the limits of the hardware.

## Why Wazuh Is The Center Of The Lab

The final version documented here uses Wazuh because it gives a security-focused learning path:

- built-in rules
- agents
- File Integrity Monitoring
- vulnerability detection
- MITRE ATT&CK mapping
- security event dashboards
- custom rule support
- Suricata alert ingestion

That means the lab can focus on evidence, detections, alerts, and investigation instead of only collecting raw logs.

## What This Documentation Covers

This documentation walks through the homelab from beginning to end:

| Area | What Is Covered |
|---|---|
| Hardware | Dell server role, Acer workstation role, hardware limits |
| Operating system | Ubuntu Server installation and first setup |
| Remote access | SSH and Tailscale access model |
| Firewall | UFW baseline and port exposure strategy |
| Docker | container platform, lab network, restart policy |
| Web targets | DVWA, Juice Shop, and Caddy JSON access logs |
| Legacy target | Metasploitable 2 in VirtualBox |
| SIEM | Wazuh Docker stack and Dell host agent |
| IDS | Suricata monitoring on lab interfaces |
| Detection | Wazuh built-in rules, custom rules, and Suricata signatures |
| Dashboards | four Wazuh dashboards for SOC-style review |
| Operations | start, stop, health checks, backups, and troubleshooting |

## What This Documentation Does Not Cover

This guide does not try to be a generic Wazuh installation guide for every environment.

It also does not cover:

- attacking systems you do not own
- exposing vulnerable services to the public internet
- building a production SIEM
- hardening every CIS benchmark finding
- running every service 24/7

The commands and explanations are written for this specific homelab. If your hardware, network, or goals are different, adjust carefully.

## What The Lab Detects

The final Wazuh setup can show several useful categories of activity:

| Detection Area | Examples |
|---|---|
| Host authentication | SSH success, SSH failure, invalid users, PAM sessions |
| Admin activity | sudo usage and important host events |
| Docker activity | container start, stop, exec, lifecycle changes |
| File integrity | added, modified, and deleted files in selected homelab paths |
| Web attacks | SQLi-like requests, XSS-like requests, command injection patterns, traversal, suspicious uploads, brute-force patterns, admin path discovery |
| Network IDS | Nmap/scan activity, Suricata signatures, destination ports, source and destination IPs |
| Legacy server logs | Metasploitable syslog and SSH activity |
| Vulnerabilities | Wazuh vulnerability inventory for the monitored host |

The lab does not claim that every alert proves a successful compromise. Most detections show suspicious activity or evidence that a test happened. That distinction matters in real SOC work.

## Learning Outcomes

By the end of the build, you should understand:

- how a homelab can be designed safely
- why logs matter before dashboards matter
- why reverse proxies help web detection
- how Wazuh agents collect host evidence
- how Suricata network alerts complement web logs
- how to validate detections with controlled tests
- how to avoid confusing "tool output" with "security evidence"

## Next Step

Continue to:

[02 - Hardware And Lab Goals](./02-hardware-and-lab-goals.md)
