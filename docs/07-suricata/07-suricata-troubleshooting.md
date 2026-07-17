# Suricata Troubleshooting

This page keeps Suricata problems separate from the clean build path.

## Suricata Does Not Start

Check service status:

```bash
sudo systemctl status --no-pager suricata
```

Check the Suricata log:

```bash
sudo tail -n 100 /var/log/suricata/suricata.log
```

Common causes:

| Symptom | Likely Cause | Fix |
|---|---|---|
| Interface not found | `vboxnet0` or `tailscale0` does not exist yet. | Start VirtualBox/Tailscale or correct the interface name. |
| Config load failed | YAML indentation or invalid rule syntax. | Run `sudo suricata -T -c /etc/suricata/suricata.yaml`. |
| Permission error | Log/rule path ownership problem. | Check `/var/log/suricata` and `/var/lib/suricata/rules`. |

## Interface Not Found

Run:

```bash
ip link show
```

You should see:

```text
vboxnet0
tailscale0
```

If `vboxnet0` is missing:

- confirm VirtualBox is installed
- confirm the host-only network exists
- return to the Metasploitable host-only network section

If `tailscale0` is missing:

- confirm Tailscale is installed
- confirm the homelab is connected to your Tailnet
- restart Tailscale if needed

## No Alerts In eve.json

Check whether Suricata is writing EVE output:

```bash
sudo tail -f /var/log/suricata/eve.json
```

If the file is empty:

- confirm Suricata is active
- confirm traffic is crossing the monitored interface
- confirm rules are loaded
- test with controlled traffic such as ping or a lab-only Nmap scan

Check loaded rule status:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

## Alerts Exist Locally But Not In Wazuh

First confirm local Suricata alerts:

```bash
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

Then confirm Wazuh logcollector is reading the file:

```bash
sudo grep -i 'suricata/eve.json' /var/ossec/logs/ossec.log
```

If Wazuh is not reading it:

- check the `<localfile>` block in `/var/ossec/etc/ossec.conf`
- confirm `<log_format>json</log_format>`
- restart the Wazuh agent

```bash
sudo systemctl restart wazuh-agent
```

## Wazuh Dashboard Shows No Suricata Events

In Wazuh Threat Hunting, search:

```text
rule.groups:suricata
```

Also try:

```text
data.homelab.source:suricata
```

If neither query shows results:

- generate a fresh Suricata alert
- wait briefly for Wazuh indexing
- widen the dashboard time range
- confirm the homelab agent is active

## Too Many Alerts

Suricata can be noisy depending on enabled rules and traffic.

For this homelab:

- keep Suricata stopped unless testing
- keep local rules narrow
- use thresholding for scan-like rules
- tune dashboards to show useful signatures, not every flow
- do not treat every low-level alert as an incident

## Nmap Scan Does Not Trigger A Custom Rule

Possible causes:

| Cause | What To Check |
|---|---|
| Scan did not cross monitored interface | Confirm `in_iface` with EVE events. |
| Scan too slow | Threshold rule may require more connections inside the time window. |
| Wrong target path | Use `tailscale0` for homelab lab-port scans and `vboxnet0` for Metasploitable-side traffic. |
| Rule not loaded | Run `sudo suricata -T -c /etc/suricata/suricata.yaml`. |

## Service Should Be Idle After Testing

After a lab session, run:

```bash
sudo systemctl stop suricata
systemctl is-active suricata
systemctl is-enabled suricata
```

Expected result:

```text
inactive
disabled
```

## Next Step

Continue to [Detection Engineering](../08-detection-engineering/01-detection-strategy.md).
