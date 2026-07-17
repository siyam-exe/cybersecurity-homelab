# Playbook: Start Suricata

Start Suricata IDS for network detection testing.

## Commands

```bash
sudo systemctl start suricata
sudo systemctl is-active suricata
sudo tail -n 5 /var/log/suricata/eve.json
```

Expected:

```text
active
```

Use Suricata only when you need IDS visibility for scans or suspicious traffic.
