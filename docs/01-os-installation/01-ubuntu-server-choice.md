# Ubuntu Server Choice

This lab starts with a clean Ubuntu Server installation on the homelab. The goal is to keep the base operating system boring, stable, and easy to maintain so the interesting work can happen in Docker, Wazuh, Suricata, and the vulnerable lab targets.

## Why Ubuntu Server

Ubuntu Server was chosen because it gives a practical balance of:

| Requirement | Why It Matters In This Lab |
|---|---|
| Long-term support | The homelab should stay stable while services are added over time. |
| Good Docker support | Most lab services run in containers. |
| Strong community documentation | Beginner troubleshooting is easier when the OS is common. |
| Lightweight install | The server does not need a desktop environment. |
| Wazuh compatibility | Wazuh agent, Docker, Suricata, rsyslog, and UFW all fit cleanly on Ubuntu. |

This guide uses Ubuntu Server 22.04 LTS because it was stable on the older homelab hardware used for the lab. A newer Ubuntu LTS release may also work, but the documented path here follows the version that was actually installed and tested.

## Why No Desktop GUI

The homelab is treated like a server, not a daily desktop. That means:

- no graphical desktop environment
- remote administration through SSH
- services managed from the terminal
- web dashboards accessed from the attack_station over Tailscale

Keeping the server headless reduces memory usage and makes the setup closer to a real small-server environment.

## Storage Choice

The homelab disk is dedicated to the homelab. During installation, the clean path is:

- use the full disk
- do not dual boot
- do not keep old partitions
- keep the partition layout simple
- disable LVM unless you specifically need it

LVM is useful in larger Linux environments, but for this beginner homelab it adds complexity without much benefit.

## Final Role Of The Operating System

After installation, Ubuntu becomes the foundation for:

- SSH administration
- Tailscale private remote access
- UFW firewall rules
- Docker containers
- VirtualBox and Metasploitable
- Wazuh agent monitoring
- rsyslog log collection
- Suricata network IDS

The OS itself is not the star of the lab. It is the clean, reliable base that everything else depends on.

## Next Step

Continue to [Create Bootable USB](./02-create-bootable-usb.md).
