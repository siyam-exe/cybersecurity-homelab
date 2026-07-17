# Security Scope And Rules

This homelab contains intentionally vulnerable systems. That makes the security boundary important.

The lab is private, controlled, and designed for learning. It should not be treated like a public service.

## Scope

This project is limited to:

- devices owned and controlled by the lab owner
- the Dell server
- the Acer workstation
- Docker containers running on the Dell
- the Metasploitable VM running on the Dell
- Tailscale-connected lab access

Do not use this documentation to test systems you do not own or do not have permission to test.

## Core Safety Rules

| Rule | Reason |
|---|---|
| Do not expose vulnerable services to the public internet | DVWA, Juice Shop, and Metasploitable are intentionally vulnerable |
| Use Tailscale for remote access | avoids router port forwarding and keeps access private |
| Keep vulnerable targets stopped unless testing | reduces unnecessary attack surface |
| Keep Metasploitable powered off unless testing | it has many old vulnerable services |
| Keep Suricata stopped unless testing | saves resources and keeps packet capture intentional |
| Keep Wazuh stopped unless needed | saves resources on the Dell |
| Do not commit secrets | GitHub documentation must not contain passwords, private keys, tokens, or real credentials |

## What Is Safe To Publish

The public documentation can include:

- architecture diagrams
- sanitized commands
- sanitized config examples
- private lab IP ranges if they help explain the design
- screenshots with sensitive values hidden
- detection logic
- dashboard design
- troubleshooting notes

The public documentation should not include:

- passwords
- API tokens
- private keys
- real login secrets
- raw exported configs containing credentials
- screenshots showing sensitive values
- anything that allows direct access to the lab

## Runtime Rule

The default state of the lab should be quiet:

```text
SSH and Tailscale stay available.
Wazuh starts only when reviewing or collecting lab alerts.
Web targets start only during web testing.
Suricata starts only during IDS testing.
Metasploitable starts only during legacy-server testing.
```

This is different from a production security environment, where monitoring normally runs all the time. In this homelab, the Dell hardware and vulnerable targets make on-demand operation the cleaner choice.

## Testing Rule

Every attack simulation should have a purpose.

Before running a test, know:

- what target is being tested
- what log source should see it
- what Wazuh rule or Suricata signature should trigger
- which dashboard should show it
- what raw evidence should exist

Example:

| Test | Expected Evidence |
|---|---|
| Nmap scan | Suricata alert and Network IDS dashboard activity |
| DVWA suspicious request | Caddy JSON log and Web Attack Lab alert |
| SSH failed login | auth log and Host & Infrastructure Security alert |
| Docker exec | Docker listener alert and host infrastructure dashboard activity |
| FIM test file change | syscheck alert and FIM panel activity |

## Documentation Rule

The main guide should show the clean working path.

Problems, failed attempts, and confusing behavior should go in troubleshooting sections. This keeps the tutorial readable while still documenting lessons learned.

Use this pattern:

```text
Main section:
  verified working commands
  expected result
  how to verify

Troubleshooting section:
  what went wrong
  why it happened
  how it was fixed
```

## Evidence Rule

Do not claim more than the logs prove.

For example:

- an SQLi-looking URL proves a suspicious request happened
- a Suricata scan alert proves scan-like network traffic happened
- an SSH failure proves an authentication attempt happened
- a Docker exec alert proves someone executed a command in a container

Those are not automatically proof of compromise. This distinction is part of learning how real SIEM investigation works.

## Section Summary

The security model is simple: keep the lab private, start vulnerable services only when needed, document the clean working path, and describe detections based on evidence instead of assumptions.

## Next Step

Continue to:

[../01-os-installation/01-ubuntu-server-choice.md](../01-os-installation/01-ubuntu-server-choice.md)
