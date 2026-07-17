# Remote Access Model

The remote access model is simple:

```text
attack_station
-> Tailscale
-> homelab
-> SSH, Wazuh, Caddy, Docker services, VirtualBox route
```

The home router is not treated as the security boundary. Tailscale is the trusted access path.

## What Should Be Reachable

| Service | Access Path | Public Internet? |
|---|---|---|
| SSH | attack_station to homelab over Tailscale | No |
| Wazuh dashboard | attack_station browser to homelab over Tailscale | No |
| Juice Shop | attack_station to Caddy on homelab over Tailscale | No |
| DVWA | attack_station to Caddy on homelab over Tailscale | No |
| Metasploitable | attack_station/Kali to VirtualBox subnet through Tailscale route | No |

## Why Not Just Use The LAN Address

The LAN address is useful at home, but it does not solve remote access when you are outside your house.

Tailscale gives one consistent access path:

- at home
- outside home
- from the attack_station
- from Kali running on the attack_station

## DHCP Reservation Instead Of Manual Netplan

For this lab, router DHCP reservation is preferred if your router supports it. That means the router always gives the homelab the same LAN address without manually editing Ubuntu's network files.

Manual Netplan static IPs are still valid, but they are easier to misconfigure and can break SSH if applied remotely.

Use manual Netplan only if you cannot create a DHCP reservation from your router.

## Service Exposure Principle

The clean rule is:

```text
Bind services only where they should be reachable,
then use UFW as an extra control layer.
```

That matters because Docker-published ports can be confusing if you assume UFW alone controls everything. The Docker port binding strategy is documented in [Docker Port Binding Strategy](./04-docker-port-binding-strategy.md).

## Next Step

Continue to [UFW Firewall Baseline](./03-ufw-firewall-baseline.md).
