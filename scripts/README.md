# Scripts And Command Bundles

This folder contains copy-paste friendly command bundles for common homelab operations.

They are written as Markdown on purpose. The full tutorial explains each command in detail, while these files provide quick operational references.

## Files

| File | Purpose |
|---|---|
| `start-lab.md` | Start Wazuh, web targets, Suricata, and Metasploitable when needed |
| `stop-lab.md` | Return the homelab to idle state after testing |
| `health-checks.md` | Check Docker, Wazuh, Suricata, Metasploitable, logs, firewall, and ports |

## Why These Are Not Shell Scripts

The commands are intentionally not packaged as executable scripts yet because some steps are optional:

- Suricata is needed only for IDS testing.
- Metasploitable is needed only for legacy-server testing.
- Web targets should start only for web app testing.
- Wazuh should be stopped again after review on limited hardware.

Keeping these as Markdown makes the choices visible for beginners.

## Full Explanations

Use these sections for detailed explanations:

```text
docs/10-operations/
docs/13-playbooks/
```
