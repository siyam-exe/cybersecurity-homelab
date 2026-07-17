# Wazuh Local Rules

This file documents sanitized examples of the custom Wazuh rules used in the homelab.

Private values, exact local-only paths, and anything environment-specific should stay out of the public repository.

Live private file:

```text
/var/ossec/etc/rules/local_rules.xml
```

## Rule ID Map

| Rule ID | Purpose |
|---:|---|
| `110100` | General Caddy access log |
| `110101` | SQLi-like web request |
| `110102` | Path traversal-like web request |
| `110103` | XSS-like web request |
| `110104` | Command injection-like request |
| `110105` | Recon/admin path discovery |
| `110106` | Suspicious upload/dangerous file request |
| `110107` | Login/brute-force candidate request |
| `110108` | Possible web brute force from same client |
| `110120` | Possible Nmap/port scan from Suricata SID |
| `110121` | Repeated network scan alerts from same source |
| `110122` | Scan-like Suricata signature text |

## Web Rule Shape

```xml
<group name="homelab,caddy,web,">
  <rule id="110100" level="3">
    <decoded_as>json</decoded_as>
    <field name="homelab.source">caddy</field>
    <description>Homelab Caddy access log</description>
  </rule>

  <rule id="110103" level="8">
    <if_sid>110100</if_sid>
    <field name="request.uri" type="pcre2">(?i)(script|%3cscript|onerror|onload|javascript:)</field>
    <description>Homelab Caddy XSS-like request</description>
    <mitre>
      <id>T1190</id>
    </mitre>
  </rule>

  <rule id="110104" level="9">
    <if_sid>110100</if_sid>
    <field name="request.uri" type="pcre2">(?i)(/vulnerabilities/exec/).*(;|%3b|&&|\|\||whoami|id|cat|nc|bash|sh)</field>
    <description>Homelab Caddy command injection-like request</description>
    <mitre>
      <id>T1190</id>
      <id>T1059</id>
    </mitre>
  </rule>

  <rule id="110105" level="6">
    <if_sid>110100</if_sid>
    <field name="request.uri" type="pcre2">(?i)(robots\.txt|admin|phpmyadmin|server-status|\.env|backup|config)</field>
    <description>Homelab Caddy recon or admin path discovery request</description>
    <mitre>
      <id>T1595</id>
    </mitre>
  </rule>

  <rule id="110106" level="8">
    <if_sid>110100</if_sid>
    <field name="request.uri" type="pcre2">(?i)(upload).*(\.php|\.jsp|\.asp|\.aspx|\.sh|shell)</field>
    <description>Homelab Caddy suspicious upload or dangerous file request</description>
    <mitre>
      <id>T1190</id>
    </mitre>
  </rule>

  <rule id="110107" level="4">
    <if_sid>110100</if_sid>
    <field name="request.uri" type="pcre2">(?i)(login|brute)</field>
    <description>Homelab Caddy login or brute-force candidate request</description>
  </rule>

  <rule id="110108" level="10" frequency="8" timeframe="120">
    <if_matched_sid>110107</if_matched_sid>
    <same_field>request.client_ip</same_field>
    <description>Homelab possible web brute force from same source</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>
```

## Scan Rule Shape

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

In live Wazuh XML, these rules use decoded field names such as `request.client_ip`, `src_ip`, and `alert.signature_id`. In Wazuh Dashboard searches, those same values normally appear as indexed `data.*` fields such as `data.request.client_ip`, `data.src_ip`, and `data.alert.signature_id`.

## Validate After Editing

Run inside the Wazuh manager container:

```bash
docker exec single-node_wazuh.manager_1 /var/ossec/bin/wazuh-analysisd -t
```

Then restart the manager container:

```bash
docker restart single-node_wazuh.manager_1
```

Command breakdown:

| Part | Meaning |
|---|---|
| `wazuh-analysisd -t` | Tests the Wazuh ruleset without relying on the dashboard. |
| `docker restart single-node_wazuh.manager_1` | Reloads the manager after the rules validate cleanly. |

Do not restart the manager with broken XML. Validate first.
