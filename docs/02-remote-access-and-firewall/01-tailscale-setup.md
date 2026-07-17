# Tailscale Setup

Tailscale is the private access layer for this homelab. The homelab does not need to expose SSH, Wazuh, DVWA, Juice Shop, or Metasploitable to the public internet. Instead, the attack_station joins the same Tailnet and reaches the homelab through its Tailscale address.

## Why Tailscale Is Used

This lab is designed to be reachable from anywhere, but not open to everyone.

Tailscale gives:

- private remote SSH access
- private Wazuh dashboard access
- private access to vulnerable lab apps
- optional subnet routing to reach Metasploitable
- no router port forwarding requirement

That makes it a good fit for a personal cybersecurity lab.

## Install Tailscale On homelab

On the homelab:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Command breakdown:

| Part | Meaning |
|---|---|
| `curl` | Downloads the official Tailscale install script. |
| `-fsSL` | Keeps the download quiet, follows redirects, and fails cleanly on errors. |
| `| sh` | Runs the downloaded installer script. |

## Bring homelab Into Your Tailnet

Run:

```bash
sudo tailscale up
```

Tailscale will print a login URL. Open it from your browser, sign in, and approve the homelab.

## Verify Tailscale

Run:

```bash
tailscale status
tailscale ip -4
```

Expected result:

- the homelab appears in your Tailnet
- the attack_station appears in your Tailnet
- the homelab has a Tailscale address, documented as `<HOMELAB_TAILSCALE_IP>`

## SSH Through Tailscale

From the attack_station:

```bash
ssh <HOMELAB_USER>@<HOMELAB_TAILSCALE_IP>
```

Once this works reliably, the firewall can be tightened so SSH is allowed through Tailscale only.

## Optional: Subnet Route For Metasploitable

Metasploitable runs inside VirtualBox on a host-only network. If you want Kali or the attack_station to reach Metasploitable remotely, the homelab must advertise the VirtualBox subnet through Tailscale.

That route is configured later in [Tailscale Subnet Routing](../05-metasploitable/05-tailscale-subnet-routing.md).

## Next Step

Continue to [Remote Access Model](./02-remote-access-model.md).
