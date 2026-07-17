# Scan Detection Rules

Scan detection in this lab uses both Suricata signatures and Wazuh correlation.

Suricata sees the network traffic. Wazuh turns repeated or important Suricata alerts into SIEM-style detections.

## Detection Sources

| Source | What It Provides |
|---|---|
| Suricata SID `1000002` | Possible port sweep toward Metasploitable. |
| Suricata SID `1000007` | Possible Nmap/port scan from Tailscale client to homelab lab ports. |
| Wazuh rule `86601` | Built-in Wazuh Suricata alert handling. |
| Wazuh rule `110120` | Custom possible Nmap/port scan detection. |
| Wazuh rule `110121` | Repeated scan correlation from the same source. |
| Wazuh rule `110122` | Scan-like Suricata signature by signature text. |

## Why This Is Not Just A Dashboard Filter

A weak dashboard would only filter for `suricata` and show whatever appears.

This lab adds detection logic:

```text
Suricata signature fires
-> Wazuh receives alert
-> Wazuh custom rule checks SID or signature text
-> Wazuh correlation rule counts repeated alerts from same source
-> dashboard shows scan detections
```

That means the dashboard is driven by detections, not just loose keyword filtering.

## Final Wazuh Scan Rules

| Rule ID | Detection | Logic |
|---:|---|---|
| `110120` | Possible Nmap / port scan | Suricata alert signature ID matches known lab scan SIDs. |
| `110121` | Repeated network scan alerts | Multiple `110120` events from the same source in a short window. |
| `110122` | Scan-like Suricata signature | Suricata signature text contains scan/Nmap/port-sweep language. |

Implemented threshold for `110121`:

| Setting | Value |
|---|---:|
| Base rule | `110120` |
| Count | `3` events |
| Window | `180` seconds |
| Same field | `src_ip` |

## Sanitized Rule Shape

```xml
<group name="homelab,ids,suricata,scan,">
  <rule id="110120" level="10">
    <if_sid>86601</if_sid>
    <field name="alert.signature_id" type="pcre2">^(1000002|1000007)$</field>
    <description>Homelab Suricata possible Nmap or port scan detection</description>
    <mitre>
      <id>T1595</id>
    </mitre>
  </rule>

  <rule id="110121" level="12" frequency="3" timeframe="180">
    <if_matched_sid>110120</if_matched_sid>
    <same_field>src_ip</same_field>
    <description>Homelab repeated network scan alerts from same source</description>
    <mitre>
      <id>T1595</id>
    </mitre>
  </rule>

  <rule id="110122" level="8">
    <if_sid>86601</if_sid>
    <field name="alert.signature" type="pcre2">(?i)(scan|nmap|port sweep)</field>
    <description>Homelab scan-like Suricata signature</description>
    <mitre>
      <id>T1595</id>
    </mitre>
  </rule>
</group>
```

Rule breakdown:

| Part | Meaning |
|---|---|
| `<if_sid>86601</if_sid>` | Start from Wazuh's Suricata alert rule. |
| `alert.signature_id` | Suricata SID field before Wazuh indexes it as `data.alert.signature_id`. |
| `1000002` | Local Metasploitable port-sweep SID. |
| `1000007` | Local Tailscale-origin scan SID. |
| `<if_matched_sid>110120</if_matched_sid>` | Count previous scan detections. |
| `<same_field>src_ip</same_field>` | Correlate repeated scan alerts from the same network source. |

The XML rule uses decoded field names such as `alert.signature_id` and `src_ip`. Dashboard searches use indexed field names such as `data.alert.signature_id` and `data.src_ip`.

## Final Test Coverage

The final validation used Nmap from `attack_station`.

| Test | Expected Result |
|---|---|
| Scan homelab lab ports over Tailscale | Suricata SID `1000007`, Wazuh `110120`, Wazuh `110121` |
| Scan Metasploitable through the approved route | Suricata alerts, Metasploitable destination ports, Wazuh Suricata alerts |
| Review dashboard | Network IDS & Legacy Server shows scan counts, signatures, source/destination IPs, ports |

## Dashboard Fields

The Network IDS & Legacy Server dashboard uses:

| Field | Use |
|---|---|
| `rule.id` | Scan and Suricata rule counts. |
| `rule.description` | Readable detection names. |
| `data.alert.signature_id` | Suricata SID. |
| `data.alert.signature` | Suricata signature text. |
| `data.src_ip` | Source IP. |
| `data.dest_ip` | Destination IP. |
| `data.dest_port` | Targeted service/port. |
| `data.in_iface` | `tailscale0` vs `vboxnet0`. |

## Next Step

Continue to [Metasploitable Detections](./04-metasploitable-detections.md).
