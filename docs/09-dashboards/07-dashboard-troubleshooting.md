# Dashboard Troubleshooting

This page keeps dashboard problems separate from the clean build path.

## A Panel Is Blank

Check:

- time range includes test data
- the relevant service was running when the test happened
- Wazuh received the logs
- the panel filter uses the correct rule IDs or fields

Useful checks:

```text
rule.id:(110103 OR 110104 OR 110105 OR 110106 OR 110108)
rule.id:(110120 OR 110121 OR 110122)
rule.groups:suricata
```

## Field Not Found

This usually means the visualization used the wrong indexed field name.

Known correct fields:

| Data | Correct Field |
|---|---|
| Docker container name | `data.docker.Actor.Attributes.name` |
| Suricata signature | `data.alert.signature` |
| Suricata SID | `data.alert.signature_id` |
| Suricata interface | `data.in_iface` |
| Web URI | `data.request.uri` |
| Web app | `data.homelab.app` |
| FIM path | `syscheck.path` |

If a field does not appear, generate a fresh event and confirm it exists in Threat Hunting first.

## Chart Shows Rule Numbers Instead Of Names

If a chart legend shows only numbers such as:

```text
110103
110104
110105
```

change the aggregation field to:

```text
rule.description
```

This makes the chart easier to read.

## Raw Logs Do Not Update When Clicking A Chart

Some OpenSearch/Wazuh dashboard interactions depend on how the visualization was built.

If clicking a chart does not filter the raw table cleanly:

- use the dashboard filter bar
- filter by `rule.id`
- filter by `rule.description`
- filter by `data.homelab.source`
- filter by `data.alert.signature_id`
- filter by `data.request.client_ip` or `data.src_ip`

Useful manual filters:

```text
rule.id:110108
data.alert.signature_id:1000007
data.homelab.app:dvwa
rule.groups:suricata
```

## Wazuh Certificate Blocks Browser Testing

The Wazuh dashboard uses HTTPS with a self-signed certificate unless you replace it.

If browser automation or a new browser refuses to open it:

- open the dashboard manually from `attack_station`
- confirm the URL is your private Tailscale dashboard URL
- accept the warning only for your own homelab
- do not expose the dashboard publicly

## Duplicate Dashboards Appear

The final guide should keep only:

```text
Homelab SOC Overview
Web Attack Lab
Network IDS & Legacy Server
Host & Infrastructure Security
```

Remove old experimental copies only after:

- final dashboards render
- raw panels work
- export/rollback exists
- object references are healthy

## Dashboard Has No Recent Data

This can be normal.

The lab is on-demand. If vulnerable targets, Suricata, and Metasploitable are stopped, the dashboards may not show new attack data.

To generate data:

1. Start Wazuh.
2. Start only the needed target.
3. Start Suricata if testing IDS.
4. Run a controlled test.
5. Refresh the dashboard.

## Next Step

Continue to [Operations](../10-operations/01-start-lab.md).
