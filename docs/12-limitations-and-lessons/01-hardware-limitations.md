# Hardware Limitations

The homelab runs on older hardware, so the design is intentionally lightweight and on-demand.

That constraint is not a failure. It shaped the lab into something more disciplined.

## Main Constraint

Wazuh is heavier than a simple log viewer.

It includes:

- Wazuh manager
- Wazuh indexer
- Wazuh dashboard
- host agent
- indexed alerts and inventories
- dashboards and saved searches

On limited hardware, running every lab service all the time is wasteful.

## On-Demand Runtime Model

The final design keeps these stopped unless needed:

| Component | Default State | Why |
|---|---|---|
| Wazuh Docker stack | stopped | saves memory and CPU |
| Suricata | stopped | avoids constant packet capture overhead |
| Juice Shop | stopped | vulnerable app should not idle unnecessarily |
| DVWA | stopped | vulnerable app should not idle unnecessarily |
| Caddy web proxy | stopped | only needed when web targets are being tested |
| Metasploitable | powered off | vulnerable legacy VM should not idle |

SSH and Tailscale stay running because they are the management path.

## What This Means For The Reader

If your hardware is stronger, you can keep more services running.

If your hardware is similar, follow the on-demand model:

```text
start only what you are testing
review the alerts
stop the lab again
```

## Tradeoff

The tradeoff is that Wazuh will not continuously monitor the lab while it is stopped.

That is acceptable here because this is a learning homelab, not a production company SOC.

## Next Step

Continue to [Wazuh Limitations](./02-wazuh-limitations.md).
