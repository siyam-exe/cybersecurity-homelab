# Nmap Scan Tests

Nmap tests prove the network IDS path.

The expected flow is:

```text
attack_station Nmap
-> Tailscale or routed lab network
-> Suricata
-> Wazuh
-> Network IDS & Legacy Server dashboard
```

## Requirements

Before testing:

- Wazuh is running
- Suricata is running
- the target is intentionally started
- the dashboard time range is set to the test window

## Scan Homelab Lab Ports

From `attack_station`:

```bash
nmap -sS -Pn -p 22,443,3002,8080 <HOMELAB_TAILSCALE_IP>
```

If your Nmap environment does not support SYN scan without elevation, use:

```bash
nmap -sT -Pn -p 22,443,3002,8080 <HOMELAB_TAILSCALE_IP>
```

Expected evidence:

| Source | Expected Result |
|---|---|
| Suricata | local scan signature or scan-like alert |
| Wazuh | Suricata alert through built-in IDS handling |
| Wazuh custom rules | `110120` and repeated scan correlation if enough alerts fire |
| Dashboard | Network IDS & Legacy Server scan panels update |

## Scan Metasploitable

Start Metasploitable first.

From `attack_station`:

```bash
nmap -sT -Pn -p 21,22,23,25,80,139,445,3306 <METASPLOITABLE_IP>
```

Expected evidence:

| Source | Expected Result |
|---|---|
| Suricata on `vboxnet0` | destination ports and scan signatures |
| Metasploitable syslog | service and SSH evidence where applicable |
| Wazuh | scan-related rules and forwarded syslog |
| Dashboard | top destination ports, source/destination IPs, Metasploitable panels |

## Useful Wazuh Searches

```text
rule.id:110120 OR rule.id:110121
```

```text
rule.groups:suricata
```

```text
data.alert.signature_id:1000007
```

## What A Good Result Looks Like

A good result does not need hundreds of alerts.

The goal is:

- scan event visible in Wazuh
- source and destination fields present
- destination ports visible
- dashboard panel updates
- raw event table explains what happened

## Next Step

Continue to [DVWA Tests](./03-dvwa-tests.md).
