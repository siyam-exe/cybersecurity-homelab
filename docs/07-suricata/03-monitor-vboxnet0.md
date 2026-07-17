# Monitor vboxnet0

`vboxnet0` is the VirtualBox host-only interface used for Metasploitable.

This interface is important because Metasploitable is intentionally isolated from the normal LAN. When `attack_station` or Kali reaches Metasploitable through the Tailscale subnet route, traffic eventually crosses `vboxnet0`.

## What This Interface Shows

`vboxnet0` is useful for:

| Activity | Expected Visibility |
|---|---|
| Ping to Metasploitable | ICMP alerts or flow records. |
| Nmap scan against Metasploitable | Port scan / connection attempts. |
| Service enumeration | Destination ports and protocol evidence. |
| Exploit attempts | Suricata signatures if the traffic matches a rule. |

Important limitation:

When traffic is routed from `attack_station` through the homelab subnet route, `vboxnet0` may show the homelab's VirtualBox-side address as the source instead of the original Tailscale client. That is why this lab also monitors `tailscale0`.

## Confirm The Interface Exists

Run:

```bash
ip link show vboxnet0
```

Expected result:

- `vboxnet0` exists
- the interface is up when the VirtualBox host-only network is active

If it does not exist, return to [Host-Only Network](../05-metasploitable/04-host-only-network.md).

## Configure HOME_NET

Open the Suricata configuration:

```bash
sudo nano /etc/suricata/suricata.yaml
```

Find the `HOME_NET` setting and use placeholders that match your lab:

```yaml
vars:
  address-groups:
    HOME_NET: "[<TAILSCALE_SUBNET>,<VBOXNET_SUBNET>,<HOMELAB_LAN_SUBNET>,<DOCKER_PRIVATE_SUBNET>]"
    EXTERNAL_NET: "any"
```

Why:

| Value | Why It Is Included |
|---|---|
| `<TAILSCALE_SUBNET>` | Covers traffic coming from Tailscale clients. |
| `<VBOXNET_SUBNET>` | Covers the Metasploitable host-only network. |
| `<HOMELAB_LAN_SUBNET>` | Covers the local LAN if you test locally. |
| `<DOCKER_PRIVATE_SUBNET>` | Covers Docker/private lab networks if needed. |

Do not paste real IP addresses into the public version of this guide. Replace the placeholders only on your private machine.

## Add vboxnet0 To AF_PACKET

In `/etc/suricata/suricata.yaml`, find the `af-packet` section and include `vboxnet0`:

```yaml
af-packet:
  - interface: vboxnet0
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    tpacket-v3: yes
```

Config breakdown:

| Line | Meaning |
|---|---|
| `interface: vboxnet0` | Tells Suricata to capture on the VirtualBox host-only interface. |
| `cluster-id: 99` | Identifies this AF_PACKET capture cluster. Use a different value per interface. |
| `cluster-type: cluster_flow` | Keeps packets from the same flow on the same worker thread. |
| `defrag: yes` | Lets Suricata handle fragmented packets. |
| `tpacket-v3: yes` | Uses the newer Linux packet capture buffer mode recommended for basic IDS setups. |

## Validate The Config

Run:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Expected result:

- config loads successfully
- no failed rules
- no interface syntax errors

## Start Suricata For A Metasploitable Test

Run:

```bash
sudo systemctl start suricata
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

In another terminal or from `attack_station`, generate controlled lab traffic:

```bash
ping <METASPLOITABLE_IP>
```

For scan testing later, use Nmap from `attack_station` or Kali only against your own Metasploitable VM.

## Verify Interface Field

Suricata events should include:

```text
"in_iface":"vboxnet0"
```

This field becomes useful in Wazuh dashboards because it separates Metasploitable/legacy-server traffic from Tailscale web-app traffic.

## Stop After Testing

Run:

```bash
sudo systemctl stop suricata
```

## Next Step

Continue to [Monitor tailscale0](./04-monitor-tailscale0.md).
