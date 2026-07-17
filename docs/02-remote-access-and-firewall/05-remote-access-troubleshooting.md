# Remote Access Troubleshooting

This page covers common SSH, Tailscale, UFW, and service reachability problems.

## SSH Works On LAN But Not Tailscale

**Check Tailscale status on the homelab:**

```bash
tailscale status
tailscale ip -4
```

**Check that SSH is listening:**

```bash
sudo systemctl status ssh
```

**Check UFW:**

```bash
sudo ufw status verbose
```

You should see SSH allowed on `tailscale0`.

## Tailscale Shows homelab Offline

Restart Tailscale:

```bash
sudo systemctl restart tailscaled
tailscale status
```

If the homelab still appears offline, run:

```bash
sudo tailscale up
```

Then re-authenticate if Tailscale asks.

## Wazuh Or Lab Apps Do Not Open From attack_station

Check the service in layers:

1. Is the attack_station connected to Tailscale?
2. Is the homelab online in `tailscale status`?
3. Is the target container running?
4. Is the Docker port bound to the intended interface?
5. Does UFW allow the port on `tailscale0`?

Useful commands:

```bash
docker ps
sudo ss -tulpn
sudo ufw status verbose
```

## Service Returns Nothing Because It Is Stopped

The vulnerable targets are intentionally stopped unless testing.

Check:

```bash
docker ps -a --filter name=juice-shop
docker ps -a --filter name=dvwa
docker ps -a --filter name=caddy-juice
```

If the containers are stopped, start them only for a lab session:

```bash
docker start juice-shop dvwa caddy-juice
```

## Do Not Remove Broad SSH Too Early

If you temporarily allowed SSH more broadly during setup, remove it only after you confirm a fresh Tailscale SSH session works.

Safe verification flow:

1. Keep the existing terminal open.
2. Open a second terminal from the attack_station.
3. SSH to `<HOMELAB_TAILSCALE_IP>`.
4. Confirm the second session works.
5. Then remove any broader SSH rule you no longer need.

## Next Step

Continue to [Docker Platform](../03-docker-platform/01-why-docker.md).
