# Playbook: Export Wazuh Dashboards

Export dashboards before major changes.

## Export From Wazuh Dashboard

From the browser:

```text
Main menu
-> Dashboards Management
-> Saved objects
-> Export
```

Select:

- dashboards
- visualizations
- saved searches

Include related objects when prompted.

## Expected Dashboards

Export these final dashboards:

```text
Homelab SOC Overview
Web Attack Lab
Network IDS & Legacy Server
Host & Infrastructure Security
```

## Store Private Backup

Save the export privately first:

```text
/home/<HOMELAB_USER>/backups/wazuh-dashboards/<TIMESTAMP>/wazuh-dashboards.ndjson
```

## Public Export Rule

Only copy the NDJSON to the public repo after searching for:

- real IP addresses
- usernames
- passwords
- tokens
- private URLs
