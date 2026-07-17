# Host Only Network

Metasploitable should use a VirtualBox host-only network. This keeps the vulnerable VM away from the normal LAN while still allowing the homelab to reach it.

## Create Or Confirm Host-Only Interface

Run:

```bash
VBoxManage list hostonlyifs
```

If `vboxnet0` does not exist, create it:

```bash
VBoxManage hostonlyif create
```

Then list again:

```bash
VBoxManage list hostonlyifs
```

## Configure The Host-Only Interface

Run:

```bash
VBoxManage hostonlyif ipconfig vboxnet0 \
  --ip <VBOXNET_HOST_IP> \
  --netmask <VBOXNET_NETMASK>
```

Use the host-only subnet planned for your lab. This guide writes it as `<VBOXNET_SUBNET>`.

## Attach Metasploitable To Host-Only Networking

Run:

```bash
VBoxManage modifyvm metasploitable2 \
  --nic1 hostonly \
  --hostonlyadapter1 vboxnet0
```

## Start And Check The VM

Run:

```bash
VBoxManage startvm metasploitable2 --type headless
VBoxManage list runningvms
```

After the VM boots, identify its address from inside the VM or from your known VirtualBox host-only configuration. In this guide, the VM address is written as:

```text
<METASPLOITABLE_IP>
```

## Test Reachability From homelab

Run:

```bash
ping -c 4 <METASPLOITABLE_IP>
```

Expected result:

- the homelab can ping Metasploitable on the host-only network
- the VM is isolated from direct public internet exposure

## Next Step

Continue to [Tailscale Subnet Routing](./05-tailscale-subnet-routing.md).
