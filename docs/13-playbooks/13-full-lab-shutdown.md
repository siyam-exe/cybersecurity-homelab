# Playbook: Full Lab Shutdown

Return the homelab to idle state after testing.

## Commands

```bash
docker stop caddy-juice juice-shop dvwa 2>/dev/null || true
sudo systemctl stop suricata
VBoxManage controlvm metasploitable2 acpipowerbutton
sleep 20
if VBoxManage list runningvms | grep -q '"metasploitable2"'; then
  VBoxManage controlvm metasploitable2 poweroff
fi
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

## Verify

```bash
docker ps
systemctl is-active suricata || true
VBoxManage list runningvms
sudo ss -tulpn
```

Expected:

- vulnerable web targets stopped
- Suricata inactive
- Metasploitable powered off
- Wazuh stopped
- SSH and Tailscale remain available
