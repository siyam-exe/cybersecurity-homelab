# Metasploitable Troubleshooting

This page covers common Metasploitable, VirtualBox, Tailscale route, and syslog issues.

## VM Does Not Start

Check the VM definition:

```bash
VBoxManage showvminfo metasploitable2
```

Check that the virtual disk path exists:

```bash
ls -lh <METASPLOITABLE_VMDK_PATH>
```

If the disk path is wrong, reattach the disk with:

```bash
VBoxManage storageattach metasploitable2 \
  --storagectl "IDE Controller" \
  --port 0 \
  --device 0 \
  --type hdd \
  --medium <METASPLOITABLE_VMDK_PATH>
```

## homelab Cannot Ping Metasploitable

Check the host-only interface:

```bash
VBoxManage list hostonlyifs
```

Check the VM network attachment:

```bash
VBoxManage showvminfo metasploitable2 | grep -i nic
```

The VM should be attached to `vboxnet0`.

## attack_station Or Kali Cannot Reach Metasploitable

Check the route in layers:

```bash
tailscale status
sudo sysctl net.ipv4.ip_forward
sudo ufw status verbose
VBoxManage list runningvms
```

Also confirm the subnet route is approved in the Tailscale admin console.

## Syslog Does Not Reach homelab

On the homelab, check rsyslog:

```bash
sudo rsyslogd -N1
sudo systemctl status rsyslog
sudo ss -ulpn
```

Check the log file:

```bash
sudo tail -n 20 /var/log/metasploitable/metasploitable.log
```

Inside Metasploitable, send a test:

```bash
logger "metasploitable syslog test"
```

## SSH To Metasploitable Fails With Old Algorithm Errors

Metasploitable is old, so modern SSH clients may reject its legacy algorithms.

For controlled lab testing only, you may need compatibility options:

```bash
ssh \
  -o HostKeyAlgorithms=+ssh-rsa,ssh-dss \
  -o PubkeyAcceptedAlgorithms=+ssh-rsa \
  <METASPLOITABLE_USER>@<METASPLOITABLE_IP>
```

Do not use this pattern for normal modern servers. It is included here because Metasploitable is intentionally legacy.

## Do Not Leave It Running

After testing:

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
VBoxManage list runningvms
```

Metasploitable should be powered off unless you are actively using it.

## Next Step

Continue to [Wazuh](../06-wazuh/01-wazuh-overview.md).
