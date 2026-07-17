# Playbook: Stop Metasploitable

Power off Metasploitable after testing.

## Commands

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
sleep 20
if VBoxManage list runningvms | grep -q '"metasploitable2"'; then
  VBoxManage controlvm metasploitable2 poweroff
fi
```

## Verify

```bash
VBoxManage list runningvms
```

Expected:

- Metasploitable is not listed

The playbook tries graceful shutdown first, then uses `poweroff` only if the VM is still running.
