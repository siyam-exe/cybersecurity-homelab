# Web App Troubleshooting

This page covers common issues with Juice Shop, DVWA, and Caddy.

## Caddy Returns Bad Gateway

**Symptom**

Caddy is reachable, but the app behind it does not load.

**Likely cause**

The backend container is stopped or still starting.

**Check:**

```bash
docker ps -a --filter name=juice-shop
docker ps -a --filter name=dvwa
docker logs juice-shop
docker logs dvwa
```

**Fix:**

```bash
docker start juice-shop dvwa
```

Then wait briefly and test again.

## attack_station Cannot Reach The Web Apps

Check in this order:

```bash
tailscale status
docker ps
sudo ufw status verbose
sudo ss -tulpn
```

The web app path needs:

- attack_station connected to Tailscale
- homelab online in Tailscale
- Caddy running
- backend app running
- UFW allowing the Caddy ports on `tailscale0`
- Docker binding attached to `<HOMELAB_TAILSCALE_IP>`

## No Caddy Logs Appear

Check the host log directory:

```bash
ls -l /var/log/homelab/caddy-juice
```

Check Caddy logs:

```bash
docker logs caddy-juice
```

Then generate a request:

```bash
curl -I http://<HOMELAB_TAILSCALE_IP>:3002/
```

Verify:

```bash
sudo tail -n 5 /var/log/homelab/caddy-juice/juice-shop-access.log
```

## Direct Juice Shop Works But Caddy Does Not

This means Juice Shop itself is probably running, but Caddy cannot reach it.

Check both containers are on `labnet`:

```bash
docker inspect -f '{{json .NetworkSettings.Networks}}' juice-shop
docker inspect -f '{{json .NetworkSettings.Networks}}' caddy-juice
```

Both should show `labnet`.

## DVWA Direct Port Missing

That is expected.

DVWA should not publish a direct host port in this setup. Use the Caddy path on port `8080`.

## Next Step

Continue to [Metasploitable](../05-metasploitable/01-why-metasploitable.md).
