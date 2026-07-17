# Detection Strategy

This section explains how the homelab turns raw logs into useful security detections.

The goal is not to create thousands of noisy alerts. The goal is to build a small, understandable SIEM workflow where every alert has a source, a reason, and a place in the dashboard.

![Wazuh log ingestion flow](../../assets/architecture/wazuh-log-ingestion-flow.png)

## Detection Philosophy

The final design uses two types of detection:

| Detection Type | Example | Why It Matters |
|---|---|---|
| Built-in Wazuh rules | SSH login failure, sudo activity, Docker container event, FIM change | These give realistic endpoint and infrastructure visibility without custom rule work. |
| Custom lab rules | XSS-like URI, command injection-like URI, web brute-force frequency, scan correlation | These make the vulnerable apps and lab attacks visible in a clean SIEM style. |

The custom rules are added only where the default visibility is not specific enough for the lab story.

## Final Detection Sources

| Source | Ingestion Path | Main Use |
|---|---|---|
| Ubuntu auth/syslog | Wazuh agent local files | SSH, PAM, sudo, system activity |
| Caddy JSON access logs | Wazuh agent local files | Juice Shop and DVWA web attacks |
| Suricata EVE JSON | Wazuh agent local file | Network IDS, scans, signatures |
| Metasploitable syslog | rsyslog to local file, then Wazuh | Legacy-server events |
| Docker events | Wazuh Docker listener | Container start, stop, exec, die, network events |
| FIM | Wazuh syscheck | Important config file changes |
| Vulnerability Detection | Wazuh inventory/state indices | Package and CVE posture |

## Why Caddy And Suricata Both Exist

Caddy and Suricata answer different questions.

| Question | Better Source |
|---|---|
| What URL was requested? | Caddy JSON logs |
| Which app was targeted, Juice Shop or DVWA? | Caddy labels |
| Which source and destination IPs were involved? | Suricata |
| Was this scan-like network behavior? | Suricata and Wazuh correlation |
| Did the vulnerable server itself log anything? | Metasploitable syslog |

For example, a SQLi-like request can be visible in both places:

```text
attack_station
-> Caddy access log
-> Wazuh web rule
```

```text
attack_station
-> Suricata on tailscale0
-> Suricata signature
-> Wazuh Suricata alert
```

That gives two kinds of evidence: application-layer request evidence and network-layer IDS evidence.

## Final Custom Wazuh Rule Groups

The final custom Wazuh detection rules live in:

```text
/var/ossec/etc/rules/local_rules.xml
```

The public sanitized examples are documented in:

```text
configs/wazuh/local-rules.md
```

Final custom detection groups:

| Area | Rule IDs | Purpose |
|---|---:|---|
| Caddy web logs | `110103` to `110108` | Web attacks and web brute-force correlation |
| Suricata scan correlation | `110120` to `110122` | Nmap/scan-style IDS correlation |

The older base web rules from the Wazuh phase are still useful:

| Rule ID | Purpose |
|---:|---|
| `110100` | General Caddy access log |
| `110101` | SQLi-like request |
| `110102` | Path traversal-like request |

The later detection expansion added the missing web and scan rules:

| Rule ID | Purpose |
|---:|---|
| `110103` | XSS-like request |
| `110104` | Command injection-like request |
| `110105` | Recon/admin path discovery |
| `110106` | Suspicious upload or dangerous file request |
| `110107` | Login/brute-force candidate request |
| `110108` | Possible web brute force frequency correlation |
| `110120` | Possible Nmap/port scan from Suricata SID |
| `110121` | Repeated network scan alerts from the same source |
| `110122` | Scan-like Suricata signature by text |

## Why Frequency Rules Are Used

Some activity should not alert strongly from one event.

One login page request is normal. Many login page requests from the same source in a short time can be brute-force behavior.

One connection attempt may be normal. Multiple scan-like Suricata alerts from the same source in a short time can indicate scanning.

Wazuh supports this with:

| Rule Option | Purpose |
|---|---|
| `frequency` | How many matching events are required. |
| `timeframe` | Time window for counting those events. |
| `if_matched_sid` | Count events that matched an earlier rule. |
| `same_field` | Require the same dynamic field value, such as the same source IP. |

In Wazuh XML rules, use the decoded dynamic field name, such as `request.client_ip` or `src_ip`. In the dashboard, the same values usually appear under indexed field names such as `data.request.client_ip` or `data.src_ip`.

That distinction matters for frequency rules: `<same_field>` expects the decoded dynamic field name, not the dashboard's `data.*` field name.

## Validation Model

Every detection should be tested in this order:

1. Start only the services needed for the test.
2. Generate one controlled event.
3. Confirm the raw log exists.
4. Confirm Wazuh receives it.
5. Confirm the expected rule fires.
6. Confirm the dashboard panel has fields to show it.
7. Stop the lab services again.

This keeps the lab controlled and avoids guessing.

## Official References

The rule style follows Wazuh's current ruleset syntax guidance:

- [Wazuh Rules Syntax](https://documentation.wazuh.com/current/user-manual/ruleset/ruleset-xml-syntax/rules.html)
- [Wazuh JSON decoder](https://documentation.wazuh.com/current/user-manual/ruleset/decoders/json-decoder.html)

## Next Step

Continue to [Web Attack Rules](./02-web-attack-rules.md).
