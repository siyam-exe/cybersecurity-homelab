# UFW Firewall Baseline

UFW is the host firewall on the homelab. It should support the Tailscale-first access model:

- deny unexpected inbound traffic
- allow normal outbound traffic
- allow SSH through Tailscale
- allow lab web ports through Tailscale
- allow Metasploitable syslog only from the VirtualBox host-only network

## Install UFW

UFW is usually already available on Ubuntu Server, but this command makes sure it is installed:

```bash
sudo apt install -y ufw
```

## Set Default Policies

Run:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw default deny routed
```

Command breakdown:

| Command | Meaning |
|---|---|
| `deny incoming` | Block inbound connections unless a rule allows them. |
| `allow outgoing` | Let the homelab reach the internet for updates, Docker pulls, and package installs. |
| `deny routed` | Block routed traffic unless a route rule allows it. |

## Allow SSH Through Tailscale

Run:

```bash
sudo ufw allow in on tailscale0 to any port 22 proto tcp
```

This allows SSH only through the Tailscale interface.

Before removing any broader SSH rule, always confirm you can open a fresh SSH session through Tailscale.

## Allow Wazuh Dashboard Through Tailscale

Run:

```bash
sudo ufw allow in on tailscale0 to any port 443 proto tcp
```

This is for the Wazuh dashboard from the attack_station browser.

## Allow Lab Web Apps Through Tailscale

Run:

```bash
sudo ufw allow in on tailscale0 to any port 3002 proto tcp
sudo ufw allow in on tailscale0 to any port 8080 proto tcp
```

These ports are for Caddy:

| Port | Lab Use |
|---:|---|
| `3002/tcp` | Juice Shop through Caddy |
| `8080/tcp` | DVWA through Caddy |

## Allow Metasploitable Syslog

Metasploitable forwards syslog to the homelab over the VirtualBox host-only interface.

Run:

```bash
sudo ufw allow in on vboxnet0 from <METASPLOITABLE_IP> to any port 514 proto udp
```

This keeps UDP syslog collection scoped to the vulnerable VM instead of accepting syslog from anywhere.

## Enable UFW

After the rules are ready:

```bash
sudo ufw enable
sudo ufw status verbose
```

Expected result:

- UFW is active
- SSH is allowed on `tailscale0`
- Wazuh dashboard is allowed on `tailscale0`
- Caddy lab ports are allowed on `tailscale0`
- Metasploitable syslog is allowed only on `vboxnet0`

## Next Step

Continue to [Docker Port Binding Strategy](./04-docker-port-binding-strategy.md).
