# Monitor tailscale0

`tailscale0` is the private remote access interface.

In this lab, it is useful because `attack_station` reaches the homelab over Tailscale. When testing Juice Shop, DVWA, Wazuh Dashboard access, or scans against homelab lab ports, `tailscale0` can preserve the original Tailscale source address better than `vboxnet0`.

## What tailscale0 Shows

`tailscale0` is useful for:

| Activity | Expected Visibility |
|---|---|
| `attack_station` to Juice Shop through Caddy | Source IP, destination port, possible HTTP signatures. |
| `attack_station` to DVWA through Caddy | Source IP, destination port, possible HTTP signatures. |
| Port checks against homelab lab ports | Scan-like Suricata alerts if the local rules match. |
| Tailscale-routed traffic before it reaches `vboxnet0` | Original Tailscale-side source context. |

## Confirm The Interface Exists

Run:

```bash
ip link show tailscale0
```

Expected result:

- `tailscale0` exists
- Tailscale is running
- the homelab is connected to the Tailnet

If it does not exist, return to [Tailscale Setup](../02-remote-access-and-firewall/01-tailscale-setup.md).

## Add tailscale0 To AF_PACKET

Open:

```bash
sudo nano /etc/suricata/suricata.yaml
```

In the `af-packet` section, keep `vboxnet0` and add `tailscale0` with a unique `cluster-id`:

```yaml
af-packet:
  - interface: vboxnet0
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    tpacket-v3: yes

  - interface: tailscale0
    cluster-id: 98
    cluster-type: cluster_flow
    defrag: yes
    tpacket-v3: yes
```

Why:

- `vboxnet0` sees Metasploitable host-only traffic
- `tailscale0` sees Tailscale-side testing traffic
- each interface gets its own `cluster-id`

## Make The Service Use Both Interfaces

Create a systemd override:

```bash
sudo mkdir -p /etc/systemd/system/suricata.service.d
sudo nano /etc/systemd/system/suricata.service.d/homelab-interfaces.conf
```

Add:

```ini
[Service]
ExecStart=
ExecStart=/usr/bin/suricata --af-packet=vboxnet0 --af-packet=tailscale0 -c /etc/suricata/suricata.yaml --pidfile /run/suricata.pid --user suricata --group suricata
```

Command/config breakdown:

| Part | Meaning |
|---|---|
| `ExecStart=` | Clears the original service start command before replacing it. |
| `/usr/bin/suricata` | Runs the installed Suricata binary. |
| `--af-packet=vboxnet0` | Captures on the VirtualBox host-only interface. |
| `--af-packet=tailscale0` | Captures on the Tailscale interface. |
| `-c /etc/suricata/suricata.yaml` | Loads the main Suricata config. |
| `--pidfile /run/suricata.pid` | Writes the service PID file where systemd expects it. |
| `--user suricata --group suricata` | Runs Suricata under the dedicated service account. |

Reload systemd:

```bash
sudo systemctl daemon-reload
```

## Validate Before Starting

Run:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Then start Suricata:

```bash
sudo systemctl start suricata
sudo systemctl status --no-pager suricata
```

Check the Suricata log:

```bash
sudo tail -n 50 /var/log/suricata/suricata.log
```

Expected signs:

- Suricata starts successfully
- worker threads are created
- both `vboxnet0` and `tailscale0` appear in startup messages

## Test With A Controlled Web Request

Start the vulnerable web targets first:

```bash
docker start juice-shop dvwa caddy-juice
```

From `attack_station`, request the lab apps through Tailscale:

```text
http://<HOMELAB_TAILSCALE_IP>:3002/
http://<HOMELAB_TAILSCALE_IP>:8080/login.php
```

On `homelab`, watch Suricata alerts:

```bash
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

Expected useful fields:

```text
src_ip
dest_ip
dest_port
in_iface
alert.signature
alert.signature_id
```

## Stop After Testing

Run:

```bash
docker stop caddy-juice juice-shop dvwa
sudo systemctl stop suricata
```

## Next Step

Continue to [Send Suricata Alerts To Wazuh](./05-send-suricata-alerts-to-wazuh.md).
