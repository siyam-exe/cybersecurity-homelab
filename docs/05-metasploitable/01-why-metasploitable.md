# Why Metasploitable

Metasploitable 2 is used as the legacy vulnerable server in this homelab. DVWA and Juice Shop are web applications, but Metasploitable gives a broader target with old services, weak configurations, and realistic network scanning behavior.

## Role In The Lab

Metasploitable helps test:

- network scanning
- port sweeps
- service enumeration
- SSH authentication events
- legacy server syslog forwarding
- Suricata IDS alerts
- Wazuh legacy-server dashboards

## Why It Runs In VirtualBox

Metasploitable is a full virtual machine, not a single containerized app.

VirtualBox gives it:

- a separate guest operating system
- its own network interface
- host-only isolation from the rest of the LAN
- a clean target for Kali or attack-station-based testing

## Safety Model

Metasploitable is intentionally vulnerable. It should not be bridged directly onto your normal network unless you fully understand the risk.

The safer model for this lab is:

```text
Metasploitable VM
-> VirtualBox host-only network
-> homelab
-> optional Tailscale subnet route for attack_station/Kali access
```

## Detection Value

Metasploitable gives Wazuh and Suricata useful evidence that web apps alone cannot provide:

| Evidence Type | Source |
|---|---|
| Failed SSH attempts | Metasploitable forwarded syslog |
| Successful SSH login events | Metasploitable forwarded syslog |
| Port scan behavior | Suricata |
| Service enumeration | Suricata and service-side logs |
| Legacy Linux service messages | Metasploitable forwarded syslog |

## Next Step

Continue to [Install VirtualBox](./02-install-virtualbox.md).
