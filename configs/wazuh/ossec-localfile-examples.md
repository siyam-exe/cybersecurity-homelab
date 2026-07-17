# Wazuh Localfile Examples

These are sanitized examples from the final homelab Wazuh agent ingestion design.

Add these blocks inside the main `<ossec_config>` section of:

```text
/var/ossec/etc/ossec.conf
```

After editing, restart the Wazuh agent:

```bash
sudo systemctl restart wazuh-agent
```

## Ubuntu Auth And Syslog

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/syslog</location>
</localfile>
```

Purpose:

- SSH success and failure
- invalid users
- PAM sessions
- sudo activity
- system service logs

## Caddy Juice Shop Access Logs

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/homelab/caddy-juice/juice-shop-access.log</location>
  <label key="homelab.source">caddy</label>
  <label key="homelab.app">juice-shop</label>
  <label key="homelab.log_type">caddy_access</label>
</localfile>
```

Purpose:

- Juice Shop web requests
- SQLi-like requests
- traversal-like requests
- XSS-like requests
- recon/admin discovery
- suspicious uploads
- web brute-force candidates

## Caddy DVWA Access Logs

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/homelab/caddy-juice/dvwa-access.log</location>
  <label key="homelab.source">caddy</label>
  <label key="homelab.app">dvwa</label>
  <label key="homelab.log_type">caddy_access</label>
</localfile>
```

Purpose:

- DVWA web requests
- command injection-like requests
- SQLi-like requests
- traversal-like requests
- login/brute-force candidates

## Metasploitable Forwarded Syslog

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/metasploitable/metasploitable.log</location>
  <label key="homelab.source">metasploitable</label>
  <label key="homelab.log_type">forwarded_syslog</label>
</localfile>
```

Purpose:

- Metasploitable service logs
- SSH/auth events from the legacy VM
- syslog evidence for the Network IDS & Legacy Server dashboard

## Suricata EVE JSON

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
  <label key="homelab.source">suricata</label>
  <label key="homelab.log_type">network_ids</label>
</localfile>
```

Purpose:

- Suricata IDS alerts
- Nmap/scan detections
- source/destination IP and port evidence
- IDS panels in Wazuh dashboards

## Verify

Run:

```bash
sudo tail -n 120 /var/ossec/logs/ossec.log
```

Look for `wazuh-logcollector` messages showing that the configured files are being analyzed.
