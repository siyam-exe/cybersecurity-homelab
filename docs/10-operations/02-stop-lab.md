# Stop Lab

This page explains how to return the homelab to its normal idle state after testing.

The safe shutdown idea is simple:

```text
stop attack targets first
-> stop IDS capture
-> stop Wazuh after reviewing alerts
```

## Normal Shutdown Order

1. Stop vulnerable web targets.
2. Stop Metasploitable.
3. Stop Suricata.
4. Export dashboards or notes if you changed anything important.
5. Stop Wazuh.
6. Confirm no lab ports are still listening unexpectedly.

## Stop Web Targets

Run:

```bash
docker stop caddy-juice juice-shop dvwa
docker ps --filter name='juice-shop' --filter name='dvwa' --filter name='caddy-juice'
```

Expected result:

- no running `juice-shop`, `dvwa`, or `caddy-juice` containers
- ports `3002/tcp` and `8080/tcp` are no longer reachable

## Stop Metasploitable

Try a graceful shutdown first:

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
sleep 20
VBoxManage list runningvms
```

If the VM is still running and you are done testing:

```bash
VBoxManage controlvm metasploitable2 poweroff
```

Use `poweroff` only when the graceful ACPI shutdown does not work.

## Stop Suricata

Run:

```bash
sudo systemctl stop suricata
systemctl is-active suricata || true
```

Expected result:

```text
inactive
```

## Stop Wazuh

After you finish reviewing alerts:

```bash
cd /home/<HOMELAB_USER>/wazuh-docker/single-node
docker-compose stop
```

This stops Wazuh containers without deleting data, volumes, indexes, or configuration.

Do not use:

```bash
docker-compose down -v
```

unless you intentionally want to delete Wazuh volumes and stored data.

## Verify Idle State

Run:

```bash
docker ps
systemctl is-active suricata || true
VBoxManage list runningvms
sudo ss -tulpn
```

Expected idle state:

| Component | Expected State |
|---|---|
| SSH | running |
| Tailscale | running |
| Wazuh containers | stopped |
| Suricata | inactive |
| Juice Shop | stopped |
| DVWA | stopped |
| Caddy web proxy | stopped |
| Metasploitable | powered off |

## Next Step

Continue to [Health Checks](./03-health-checks.md).
