# Common Maintenance

This page covers routine maintenance for the homelab.

Do maintenance when you are not in the middle of a detection test, because updates and restarts can create extra alerts.

## Update Ubuntu Packages

Run:

```bash
sudo apt update
sudo apt list --upgradable
sudo apt upgrade
```

Read the package list before approving upgrades. If the system wants to restart important services, finish any lab session first.

## Check Disk Usage

Run:

```bash
df -h
sudo du -sh /var/log/homelab /var/log/metasploitable /var/log/suricata 2>/dev/null
```

If log directories are growing too quickly, review logrotate and reduce unnecessary test noise.

## Update Suricata Rules

Run:

```bash
sudo suricata-update
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Only restart Suricata if you are actively using it:

```bash
sudo systemctl restart suricata
```

## Check Wazuh Health

When Wazuh is running:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose ps
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
```

## Review Dashboard Exports

Export dashboards after major dashboard edits.

Do not wait until something breaks. Dashboard work is time-consuming, so keep a private rollback export.

## Review Public Docs Before Publishing

Before committing documentation:

```bash
rg -n "\b[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\b" .
rg -n "password|token|secret|private key" .
```

Review matches manually. Not every match is bad, but no real secrets or private addresses should be published.

## Next Step

Continue to [Attack Simulation](../11-attack-simulation/01-testing-rules.md).
