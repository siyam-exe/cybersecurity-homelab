# Dashboard Import And Export

Wazuh dashboards should be exported after they are working, and imported when rebuilding the lab or sharing the sanitized dashboard pack.

Exports are useful because:

- dashboards take time to build
- visualizations can break if fields are changed
- a rollback file protects the work
- GitHub readers can understand the final dashboard objects

## Dashboard Add Options

There are two clean ways to add the dashboards:

| Method | Best Use |
|---|---|
| Import saved objects | Fastest way to restore the final dashboards after they are already built and exported. |
| Build manually in the UI | Best for learning how each visualization is made or rebuilding from scratch. |

For this guide, the preferred final workflow is:

```text
build once
-> verify panels
-> export sanitized NDJSON
-> import the NDJSON when recreating the dashboard set
```

## Import Dashboards From NDJSON

Use this when you already have a sanitized dashboard export.

From Wazuh Dashboard:

```text
Main menu
-> Dashboards Management
-> Saved objects
-> Import
-> select wazuh-dashboards.ndjson
```

During import:

1. Include related objects when prompted.
2. Review any overwrite warnings.
3. If this is a clean setup, allow the import.
4. If dashboards already exist, export a backup first before overwriting.

Expected imported dashboards:

```text
Homelab SOC Overview
Web Attack Lab
Network IDS & Legacy Server
Host & Infrastructure Security
```

Expected object IDs:

```text
homelab-soc-overview
web-attack-lab
network-ids-legacy-server
host-infrastructure-security
```

## Create Dashboards Manually

If you are building them from scratch, create the dashboards in this order:

1. Homelab SOC Overview
2. Web Attack Lab
3. Network IDS & Legacy Server
4. Host & Infrastructure Security

From Wazuh Dashboard:

```text
Main menu
-> Dashboards
-> Dashboards
-> Create dashboard
```

Then add panels:

```text
Edit
-> Add
-> Create visualization
```

Use the Wazuh alerts index pattern:

```text
wazuh-alerts-*
```

## Manual Panel Build Order

When building manually, create panels in this order:

| Dashboard | Start With | Then Add |
|---|---|---|
| Homelab SOC Overview | KPI counters | MITRE charts, detection categories, source/target charts, notable alerts |
| Web Attack Lab | Web attack KPI counters | Attack timeline, attack type breakdown, app split, URLs, status codes, raw web logs |
| Network IDS & Legacy Server | IDS and scan KPI counters | Signatures, interfaces, ports, IPs, Metasploitable logs, raw IDS logs |
| Host & Infrastructure Security | SSH/sudo/Docker/FIM counters | Docker lifecycle, auth sources, FIM paths, raw infrastructure logs |

Use the field map from [Dashboard Design](./01-dashboard-design.md) when choosing aggregations.

## Add Raw Investigation Tables

Raw tables are useful for investigation, but they should not dominate the dashboard.

Create saved searches for:

| Saved Search | Purpose |
|---|---|
| Raw web attack logs | Exact web attack events and URIs |
| Raw IDS and legacy logs | Suricata and Metasploitable events |
| Raw infrastructure logs | SSH, sudo, Docker, FIM, system events |
| Recent notable alerts | High-signal SOC overview table |

Add only the useful raw tables to dashboards.

## What To Export

Export these final dashboard objects:

```text
Homelab SOC Overview
Web Attack Lab
Network IDS & Legacy Server
Host & Infrastructure Security
```

Include related visualizations and saved searches when exporting.

## Export From The UI

From Wazuh Dashboard:

```text
Main menu
-> Dashboards Management
-> Saved objects
-> Export
```

Select:

```text
dashboards
visualizations
saved searches
```

Save the result as:

```text
wazuh-dashboards.ndjson
```

## Import Conflict Handling

If Wazuh says an object already exists:

1. Stop and export the current dashboard objects first.
2. Decide whether the current objects are older or newer.
3. If the import is the intended final version, allow overwrite.
4. After import, open all four dashboards and verify panels.

Do not delete working dashboards until you have an export backup.

## Store A Sanitized Export

Public sanitized exports belong in:

```text
exports/wazuh-dashboards/
```

Before committing an export:

- open the NDJSON in a text editor
- search for real IP addresses
- search for real usernames
- search for passwords or tokens
- search for private URLs
- remove or sanitize anything sensitive

## Rollback Model

Keep a private rollback export before major dashboard edits:

```text
/home/<HOMELAB_USER>/backups/wazuh-dashboards/<TIMESTAMP>/wazuh-before-dashboard-change.ndjson
```

Use a timestamp so each backup is unique.

## Verify Imported Dashboards

After import:

1. Open each dashboard.
2. Set time range to a period with test data.
3. Check for blank panels.
4. Check for broken field errors.
5. Check that raw investigation tables load.
6. Check that chart legends use readable names where possible.
7. Confirm the four final dashboard names appear without duplicate experimental copies.

Final dashboard object IDs:

```text
homelab-soc-overview
web-attack-lab
network-ids-legacy-server
host-infrastructure-security
```

## Next Step

Continue to [Dashboard Troubleshooting](./07-dashboard-troubleshooting.md).
