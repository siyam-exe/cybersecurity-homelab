# Playbook: Start Metasploitable

Start the Metasploitable 2 VM for legacy-server testing.

## Commands

```bash
VBoxManage startvm metasploitable2 --type headless
VBoxManage list runningvms
```

## Verify

```bash
ping -c 2 <METASPLOITABLE_IP>
```

Expected:

- VM appears in the running VM list
- `homelab` can reach `<METASPLOITABLE_IP>`

If your VM name is different, replace `metasploitable2` with your VirtualBox VM name.
