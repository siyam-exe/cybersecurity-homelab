# Tailscale Subnet Routing

Subnet routing lets the attack_station or Kali reach Metasploitable through the homelab. This is useful when your attack machine is not physically on the homelab's VirtualBox host-only network.

The flow is:

```text
attack_station or Kali
-> Tailscale
-> homelab subnet router
-> vboxnet0
-> Metasploitable
```

## Enable IP Forwarding On The Homelab

Run:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Command breakdown:

| Part | Meaning |
|---|---|
| `sysctl -w` | Changes a kernel setting immediately for the current boot. |
| `net.ipv4.ip_forward=1` | Allows the homelab to route IPv4 traffic between interfaces. |

To make it persistent, create:

```bash
sudo nano /etc/sysctl.d/99-homelab-routing.conf
```

Add:

```conf
net.ipv4.ip_forward=1
```

Apply:

```bash
sudo sysctl --system
```

This reloads the persistent setting from `/etc/sysctl.d/` so the route survives reboots.

## Advertise The VirtualBox Subnet

Run:

```bash
sudo tailscale set --advertise-routes=<VBOXNET_SUBNET>
```

Command breakdown:

| Part | Meaning |
|---|---|
| `tailscale set` | Updates the existing Tailscale node configuration without re-running the full login flow. |
| `--advertise-routes=<VBOXNET_SUBNET>` | Tells Tailscale that this host can route traffic to the VirtualBox host-only subnet. |

Then open the Tailscale admin console from your browser and approve the route for the homelab.

Older guides may use `tailscale up --advertise-routes=...`. The cleaner current approach is `tailscale set --advertise-routes=...`, especially when Tailscale is already installed and logged in.

## Add UFW Route Rules

Run:

```bash
sudo ufw route allow in on tailscale0 out on vboxnet0 to <VBOXNET_SUBNET>
sudo ufw route allow in on vboxnet0 out on tailscale0 from <VBOXNET_SUBNET>
sudo ufw status verbose
```

These rules allow the Tailscale-to-VirtualBox path without opening Metasploitable to the public internet.

Command breakdown:

| Part | Meaning |
|---|---|
| `ufw route allow` | Creates a forwarding rule instead of a normal local service rule. |
| `in on tailscale0 out on vboxnet0` | Allows traffic entering from Tailscale to leave toward the VirtualBox network. |
| `in on vboxnet0 out on tailscale0` | Allows reply traffic from the VirtualBox network back to Tailscale. |
| `to` / `from <VBOXNET_SUBNET>` | Limits the forwarding path to the Metasploitable lab subnet. |
| `ufw status verbose` | Shows the active firewall state and route rules. |

## Test From attack_station Or Kali

From attack_station or Kali:

```bash
ping <METASPLOITABLE_IP>
```

You can also test a single port with a tool such as `nmap` or PowerShell, but keep testing controlled and inside your own lab.

## Next Step

Continue to [Syslog Forwarding](./06-syslog-forwarding.md).
