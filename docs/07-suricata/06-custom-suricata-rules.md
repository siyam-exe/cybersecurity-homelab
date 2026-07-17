# Custom Suricata Rules

The ET Open ruleset gives Suricata broad IDS coverage, but this homelab also uses a small local rules file for predictable lab detections.

Local rules are useful because the lab has specific testing goals:

- prove Suricata-to-Wazuh ingestion works
- detect controlled Metasploitable scans
- detect Tailscale-origin scans against lab ports
- create readable `HOMELAB` signatures for dashboards
- compare Caddy/Wazuh web detections with Suricata/Wazuh network detections

## Local Rules File

Create the local rule file:

```bash
sudo nano /var/lib/suricata/rules/homelab.rules
```

Then include it from `/etc/suricata/suricata.yaml`:

```yaml
default-rule-path: /var/lib/suricata/rules

rule-files:
  - suricata.rules
  - homelab.rules
```

Config breakdown:

| Line | Meaning |
|---|---|
| `default-rule-path` | Points Suricata to the directory where active rule files live. |
| `suricata.rules` | Combined rule file from `suricata-update`. |
| `homelab.rules` | Local lab-specific rules. |

## Lab Rule Plan

Use this SID plan:

| SID | Detection Purpose |
|---:|---|
| `1000001` | ICMP ping test for Suricata/Wazuh proof. |
| `1000002` | Possible port sweep against Metasploitable on `vboxnet0`. |
| `1000003` | Tailscale connection to vulnerable web app ports. |
| `1000004` | SQLi-like web request over Tailscale. |
| `1000005` | `/etc/passwd` request over Tailscale. |
| `1000006` | Encoded traversal attempt over Tailscale. |
| `1000007` | Possible Nmap/port scan from Tailscale client to homelab lab ports. |

## Sanitized Example Rules

The exact private rule file should use your private addresses and ports. The public version uses placeholders.

```suricata
alert icmp any any -> $HOME_NET any (msg:"HOMELAB Suricata ICMP ping test"; sid:1000001; rev:1;)

alert tcp any any -> <METASPLOITABLE_IP> any (msg:"HOMELAB possible port sweep to Metasploitable"; flags:S; threshold:type both, track by_src, count 10, seconds 60; sid:1000002; rev:1;)

alert tcp any any -> $HOME_NET [3002,8080] (msg:"HOMELAB Tailscale connection to vulnerable web app"; flow:to_server,established; sid:1000003; rev:1;)

alert http any any -> $HOME_NET any (msg:"HOMELAB Suricata web SQLi-like request over Tailscale"; flow:to_server,established; http.uri; pcre:"/('|%27|union|select|or%20[0-9]+=|--)/Ui"; sid:1000004; rev:1;)

alert http any any -> $HOME_NET any (msg:"HOMELAB Suricata web /etc/passwd request over Tailscale"; flow:to_server,established; http.uri; content:"/etc/passwd"; nocase; sid:1000005; rev:1;)

alert http any any -> $HOME_NET any (msg:"HOMELAB Suricata encoded traversal attempt over Tailscale"; flow:to_server,established; http.uri; content:"%2e"; nocase; content:"%2f"; nocase; sid:1000006; rev:1;)

alert tcp any any -> $HOME_NET any (msg:"HOMELAB possible Nmap or port scan from Tailscale client to homelab"; flags:S; threshold:type both, track by_src, count 8, seconds 60; sid:1000007; rev:1;)
```

Rule breakdown:

| Part | Meaning |
|---|---|
| `alert` | Generate an alert when the rule matches. |
| `icmp`, `tcp`, `http` | Protocol/app-layer inspected by the rule. |
| `any any -> ...` | Source and destination direction. |
| `msg:"..."` | Human-readable signature name shown in Wazuh. |
| `flow:to_server,established` | Focuses HTTP rules on established client-to-server traffic. |
| `http.uri` | Applies content matching to the HTTP URI. |
| `pcre:"..."` | Uses a regular expression for SQLi-like patterns. |
| `threshold` | Prevents one packet from alerting alone; useful for scan-like behavior. |
| `track by_src` | Counts repeated events from the same source. |
| `sid` | Unique Suricata signature ID. |
| `rev` | Rule revision. Increment when you change the rule. |

## Validate Rules

Run:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Expected result:

- configuration loads successfully
- `homelab.rules` is included
- no failed rules

If a rule fails, do not start Suricata yet. Fix the rule syntax first.

## Restart For Testing

Run:

```bash
sudo systemctl restart suricata
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

## How These Rules Appear In Wazuh

In Wazuh, the most useful fields are:

| Wazuh Field | Meaning |
|---|---|
| `data.alert.signature_id` | Suricata SID, such as `1000007`. |
| `data.alert.signature` | Readable rule message. |
| `data.in_iface` | `tailscale0` or `vboxnet0`. |
| `data.src_ip` | Source IP. |
| `data.dest_ip` | Destination IP. |
| `data.dest_port` | Targeted port. |

The later detection-engineering section uses these fields for Wazuh correlation rules, such as repeated scan detection.

## Copy For Public Configs

A sanitized version of this rule set is also stored in:

```text
configs/suricata/local.rules
```

Use it as a template. Replace placeholders only in your private homelab copy.

## Stop After Testing

Run:

```bash
sudo systemctl stop suricata
```

## Next Step

Continue to [Suricata Troubleshooting](./07-suricata-troubleshooting.md).
