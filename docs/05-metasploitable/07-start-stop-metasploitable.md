# Start Stop Metasploitable

Metasploitable should be powered on only when you are actively using it for testing.

## Start The VM

Run on the homelab:

```bash
VBoxManage startvm metasploitable2 --type headless
```

Verify:

```bash
VBoxManage list runningvms
```

## Test Reachability

From the homelab:

```bash
ping -c 4 <METASPLOITABLE_IP>
```

From the attack_station or Kali, if subnet routing is enabled:

```bash
ping <METASPLOITABLE_IP>
```

## Stop The VM Gracefully

Run:

```bash
VBoxManage controlvm metasploitable2 acpipowerbutton
```

Wait briefly, then check:

```bash
VBoxManage list runningvms
```

## Force Power Off Only If Needed

If the VM does not stop after a reasonable wait:

```bash
VBoxManage controlvm metasploitable2 poweroff
```

Use forced poweroff only when needed. Graceful shutdown is cleaner.

## Confirm It Is Off

Run:

```bash
VBoxManage list runningvms
```

Expected result:

- `metasploitable2` is not listed
- the vulnerable VM is no longer running

## Next Step

Continue to [Metasploitable Troubleshooting](./08-metasploitable-troubleshooting.md).
