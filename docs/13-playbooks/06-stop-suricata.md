# Playbook: Stop Suricata

Stop Suricata when network IDS testing is finished.

## Commands

```bash
sudo systemctl stop suricata
sudo systemctl is-active suricata || true
```

Expected:

```text
inactive
```
