# Stop Lab Command Bundle

This is a copy-paste friendly command bundle for returning the homelab to idle state.

Use the detailed explanation in:

```text
docs/10-operations/02-stop-lab.md
docs/13-playbooks/13-full-lab-shutdown.md
```

## Stop Vulnerable Web Targets

```bash
docker stop caddy-juice juice-shop dvwa 2>/dev/null || true
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

## Stop Suricata

```bash
sudo systemctl stop suricata
sudo systemctl is-active suricata || true
```

Expected:

```text
inactive
```

## Stop Metasploitable

Try graceful shutdown first:

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
sleep 20
```

Force power off only if the VM is still running:

```bash
if VBoxManage list runningvms | grep -q '"metasploitable2"'; then
  VBoxManage controlvm metasploitable2 poweroff
fi
```

Verify:

```bash
VBoxManage list runningvms
```

## Stop Wazuh

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

For Docker Compose v2:

```bash
docker compose stop
```

## Final Idle Check

```bash
docker ps
sudo systemctl is-active suricata || true
VBoxManage list runningvms
sudo ss -tulpn
```

Expected:

- SSH and Tailscale remain available
- Wazuh containers are stopped
- vulnerable web targets are stopped
- Suricata is inactive
- Metasploitable is powered off
