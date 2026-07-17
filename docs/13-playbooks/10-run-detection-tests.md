# Playbook: Run Detection Tests

Generate safe events and confirm Wazuh detections.

## Start Required Services

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose up -d
docker start juice-shop dvwa caddy-juice
sudo systemctl start suricata
```

Start Metasploitable only if testing legacy-server detections:

```bash
VBoxManage startvm metasploitable2 --type headless
```

## Docker Event Test

```bash
docker restart juice-shop
```

Expected Wazuh evidence:

```text
rule.id:87903 OR rule.id:87904
```

## FIM Test

```bash
FIM_TEST="/home/<HOMELAB_USER>/caddy/juice-shop/config/.wazuh-fim-test"
date -Is > "$FIM_TEST"
printf 'modified %s\n' "$(date -Is)" >> "$FIM_TEST"
rm -f "$FIM_TEST"
```

Expected Wazuh evidence:

```text
rule.groups:syscheck
```

## Web Rule Test

```bash
curl "http://<HOMELAB_TAILSCALE_IP>:3002/?q=%27%20OR%201%3D1--"
curl "http://<HOMELAB_TAILSCALE_IP>:8080/%2e%2e/%2e%2e/etc/passwd"
```

Expected Wazuh evidence:

```text
rule.id:110101 OR rule.id:110102
```

## Nmap Scan Test

From `attack_station`:

```bash
nmap -sT -Pn -p 22,443,3002,8080 <HOMELAB_TAILSCALE_IP>
```

Expected Wazuh evidence:

```text
rule.id:110120 OR rule.id:110121 OR rule.groups:suricata
```

## Cleanup

```bash
docker stop caddy-juice juice-shop dvwa
sudo systemctl stop suricata
```
