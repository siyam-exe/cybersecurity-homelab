# Why Docker

Docker is used for the services that should be easy to start, stop, rebuild, and isolate from the base Ubuntu system.

In this lab, Docker runs:

- Wazuh stack
- OWASP Juice Shop
- DVWA
- Caddy reverse proxy

VirtualBox is used separately for Metasploitable because Metasploitable is a full vulnerable virtual machine, not just a web app.

## Why Containers Fit This Lab

| Need | Docker Benefit |
|---|---|
| Start vulnerable apps only when needed | Containers can stay stopped until testing. |
| Keep Ubuntu cleaner | App dependencies stay inside containers. |
| Rebuild broken apps quickly | Remove and recreate a container instead of repairing the host. |
| Control ports clearly | Docker bindings define what is exposed. |
| Monitor lifecycle events | Wazuh can detect Docker start, stop, exec, and network events. |

## What Docker Does Not Replace

Docker does not replace:

- UFW firewall rules
- Tailscale remote access control
- VirtualBox for Metasploitable
- Wazuh detection logic
- log rotation and host log permissions

It is only the runtime platform for containerized services.

## Lab Container Rule

The vulnerable targets should not automatically start after reboot.

That means their restart policy should be:

```text
restart=no
```

This keeps the homelab safe and quiet when you are not intentionally testing.

## Next Step

Continue to [Install Docker](./02-install-docker.md).
