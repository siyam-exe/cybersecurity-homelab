# Wazuh Dashboard Exports

Store sanitized Wazuh dashboard exports here.

Recommended filename:

```text
wazuh-dashboards.ndjson
```

## Export Contents

The export should include:

- `Homelab SOC Overview`
- `Web Attack Lab`
- `Network IDS & Legacy Server`
- `Host & Infrastructure Security`
- related visualizations
- related raw investigation saved searches

## Import Path

From Wazuh Dashboard:

```text
Main menu
-> Dashboards Management
-> Saved objects
-> Import
-> select wazuh-dashboards.ndjson
```

After importing, open all four dashboards and verify that the panels render correctly.

## Before Committing

Open the NDJSON file and search for:

- real IP addresses
- real usernames
- passwords
- API tokens
- private URLs
- private hostnames if you do not want them public

Replace environment-specific values with placeholders before committing.

## Final Dashboard IDs

```text
homelab-soc-overview
web-attack-lab
network-ids-legacy-server
host-infrastructure-security
```
