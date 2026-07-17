# Testing Rules

Attack simulation in this guide is only for the private lab.

The goal is to generate clear security evidence, not to cause damage.

## Testing Scope

Only test:

- the homelab server
- DVWA
- OWASP Juice Shop
- Metasploitable
- lab services you own and intentionally started

Do not scan or attack:

- public internet targets
- neighbors' networks
- school or workplace systems
- random IP ranges
- services you do not own

## Before Each Test

Confirm:

1. Wazuh is running.
2. The homelab agent is active.
3. The target service is intentionally started.
4. Suricata is running if the test needs IDS evidence.
5. The dashboard time range includes the test window.

## Test Record Template

For each test, record:

| Field | Example |
|---|---|
| Test name | Nmap scan against Metasploitable |
| Source | `attack_station` |
| Target | `<METASPLOITABLE_IP>` |
| Log source | Suricata EVE JSON and Metasploitable syslog |
| Expected Wazuh rule/signature | `110120`, `110121`, Suricata SID |
| Dashboard | Network IDS & Legacy Server |
| Result | Worked, partial, or not detected |

## Keep Tests Small

Run one test at a time.

This makes it easier to answer:

```text
Which action caused this alert?
```

If too many tests run together, the dashboard still shows alerts, but the learning value drops.

## After Each Test

Check in this order:

1. Raw source log.
2. Wazuh Threat Hunting event.
3. Correct custom or built-in rule.
4. Correct dashboard panel.
5. Raw table evidence.

## Next Step

Continue to [Nmap Scan Tests](./02-nmap-scan-tests.md).
