# Playbook: Run Health Checks

Check whether the lab is ready for testing.

## General Checks

```bash
hostname
uptime
tailscale status
sudo ufw status verbose
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
systemctl is-active wazuh-agent
systemctl is-active suricata || true
VBoxManage list runningvms
sudo ss -tulpn
```

## Wazuh Checks

Run only when Wazuh is started:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose ps
docker exec single-node_wazuh.manager_1 /var/ossec/bin/agent_control -l
curl -k -I https://<HOMELAB_TAILSCALE_IP>/
```

## Expected Idle State

| Component | Expected |
|---|---|
| SSH | active |
| Tailscale | connected |
| Wazuh | stopped unless testing |
| Suricata | inactive unless testing |
| web targets | stopped unless testing |
| Metasploitable | powered off unless testing |
