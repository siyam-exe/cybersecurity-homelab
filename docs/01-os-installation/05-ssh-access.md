# SSH Access

SSH is the first remote administration layer. It lets the attack_station open a terminal session on the homelab without needing to sit at the physical machine.

At this early stage, SSH can be tested over the local network. Later, the remote access section moves administration to Tailscale so the homelab can be reached privately from anywhere.

## Find The Homelab Address

On the homelab, run:

```bash
ip addr
```

Look for the Ethernet interface and note the address assigned by your router. In this guide, that value is written as:

```text
<HOMELAB_LAN_IP>
```

## SSH From attack_station

From PowerShell, Windows Terminal, or another terminal on the attack_station:

```bash
ssh <HOMELAB_USER>@<HOMELAB_LAN_IP>
```

Command breakdown:

| Part | Meaning |
|---|---|
| `ssh` | Open a secure shell session. |
| `<HOMELAB_USER>` | The Linux user created during Ubuntu installation. |
| `<HOMELAB_LAN_IP>` | The homelab's reserved LAN address, written as a neutral placeholder. |

The first time you connect, SSH may ask whether you trust the server fingerprint. Confirm only if the address is your homelab.

## Verify The Session

After logging in, run:

```bash
hostname
whoami
```

Expected result:

- `hostname` shows the homelab hostname
- `whoami` shows your `<HOMELAB_USER>`
- commands are running on the homelab, not the attack_station

## Why This Is Not The Final Access Model

Local SSH is useful during the first setup, but the final lab uses Tailscale for private remote access. That gives you a consistent way to reach the homelab from the attack_station whether you are at home or away.

## Next Step

Continue to [Timezone And Hostname](./06-timezone-and-hostname.md).
